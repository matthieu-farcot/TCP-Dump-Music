# TCP-Dump-Music

Project mixing online traffic with sounds and maybe lights


## Idea
Generate random but harmonical music based on real time traffic capture. Mess around with the idea.

## Note
For Mardown tips, see https://guides.github.com/features/mastering-markdown/
* Editing is easy
* Editing is super easy

## Links for ressources:
### MIDI related
https://en.wikipedia.org/wiki/General_MIDI

### Project related
  * https://github.com/nickpegg/tcpdump-tunes
  * https://github.com/vishnubob/python-midi/
  * http://www.tcpdump.org
  * https://www.r-bloggers.com/programming-instrumental-music-from-scratch/
  * https://stackoverflow.com/questions/11059801/how-can-i-write-a-midi-file-with-python

### Installation

### Example output and files

<pre><code>
#!/usr/bin/env python

# Parses the text output of tcpdump (with no -v) from stdin, makes music

import argparse
import re
import sys
from datetime import datetime

import midi


MIN_SPACING = 60
MIN_LENGTH = 2


def main():
    line_re = r"(?P<timestamp>\d{2}:\d{2}:\d{2}.\d{6}) IP (?P<src>\d+\.\d+\.\d+\.\d+)\.(?P<sport>\d+) > (?P<dst>\d+\.\d+\.\d+\.\d+)\.(?P<dport>\d+): (?:Flags \[(?P<flags>[SFPRUWE\.]+)\])?.+?length (?P<length>\d+)"

    arg_parser = argparse.ArgumentParser(description="MIDI from tcpdump")
    arg_parser.add_argument('-f',
                            help="Output file, omit or give - for stdout",
                            metavar='file',
                            default='-')
    args = arg_parser.parse_args()

    if args.f == '-':
        output_file = sys.stdout
    else:
        output_file = open(args.f, 'w')

    pattern = midi.Pattern()
    track = midi.Track()
    pattern.append(track)

    last_time = datetime.now()
    line = sys.stdin.readline()
    while line:
        match = re.match(line_re, line)
        if match:
            data = match.groupdict()

            # Get the difference between now and the last event
            now = datetime.strptime(data['timestamp'], '%H:%M:%S.%f')

            spacing = int((now - last_time).microseconds / 1000.0)
            last_time = now

            if spacing < MIN_SPACING:
                spacing = MIN_SPACING

            # Determine note length based on packet length
            pkt_length = int(data.get('length', 0))
            note_length = int(pow(pkt_length, 0.75))

            if note_length < MIN_LENGTH:
                note_length = MIN_LENGTH

            # determine the note to play based on TCP flags
            note = midi.C_4
            flags = data.get('flags', '')
            if flags:
                if 'S' in flags:
                    note = midi.D_4
                elif '.' in flags:
                    note = midi.C_4
                elif 'F' in flags:
                    note = midi.F_4
                elif 'R' in flags:
                    note = midi.G_4

            # Determine the octave based on the src and dst IPs
            octave = hash((data['src'], data['dst'])) % 6 + 2
            note = note + octave * 12

            # Finally, append the note to the track

            track.append(midi.NoteOnEvent(tick=spacing, velocity=50, pitch=note))
            track.append(midi.NoteOffEvent(tick=note_length, pitch=note))
	    track.append(midi.ProgramChangeEvent(data=[53]))

        line = sys.stdin.readline()

    # Dump MIDI track to stdout

    track.append(midi.EndOfTrackEvent(tick=1))
    midi.write_midifile(output_file, pattern)
    

####

    track2 = midi.Track()
    pattern.append(track2)

    last_time = datetime.now()
    line = sys.stdin.readline()
    while line:
        match = re.match(line_re, line)
        if match:
            data = match.groupdict()

            # Get the difference between now and the last event
            now = datetime.strptime(data['timestamp'], '%H:%M:%S.%f')

            spacing = int((now - last_time).microseconds / 1000.0)
            last_time = now

            if spacing < MIN_SPACING:
                spacing = MIN_SPACING

            # Determine note length based on packet length
            pkt_length = int(data.get('length', 0))
            note_length = int(pow(pkt_length, 0.75))

            if note_length < MIN_LENGTH:
                note_length = MIN_LENGTH

            # determine the note to play based on TCP flags
            note = midi.E_5
            flags = data.get('flags', '')
            if flags:
                if 'S' in flags:
                    note = midi.E_5
                elif '.' in flags:
                    note = midi.E_5
                elif 'F' in flags:
                    note = midi.E_5
                elif 'R' in flags:
                    note = midi.E_5

            # Determine the octave based on the src and dst IPs
            octave = hash((data['src'], data['dst'])) % 6 + 2
            note = note + octave * 22

            # Finally, append the note to the track

            track.append(midi.NoteOnEvent(tick=spacing, velocity=50))
            track.append(midi.NoteOffEvent(tick=note_length, pitch=note))
	    track.append(midi.ProgramChangeEvent(data=[14]))

        line = sys.stdin.readline()

    # Dump MIDI track to stdout

    track.append(midi.EndOfTrackEvent(tick=1))
    midi.write_midifile(output_file, pattern)

    return 0


if __name__ == '__main__':
    exit(main())
</code></pre>
