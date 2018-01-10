# Documentation of the python script

## Explanation of changed code

* The octave multiplier set to 5 (note = note + octave * 5) (Line 76)
  -> Sounds a bit darker than the natural instrument sound, but different pitches sound better.  
  -> The pitch that seems to be the most reoccuring is rather high pitched and therefore sounds less bad with this octave setting.  
  -> Additionally, a max octave value has to be set, to prevent extremely high pitched notes (Line 15).  
  -> Next, a if statement has been set, that whenever the result of (note = note + octave * 5) is higher than 85, it is being limited to 85.  

* Added a second instrument (Line 35/36/86/87/88/96)
  -> The MIDI File consists of one single pattern, but the pattern can consist of multiple tracks.  
  -> In order to add a second track with a different instrument to the pattern we declare a second track first (Line 35).  
  -> Then we append the created track to the pattern (Line 36).  
  -> Just like with the first track, a NoteOnEvent, NoteOffEvent and ProgramChangeEvent (Instrument) has to be assigned (Line 68-88).  
  -> Finally a EndOfTrackEvent has to be assigned to the newly created track(Line 96).  
  -> More instruments can easily be added with the same principle.  

## Short Explanation of specific lines of code
  * MAX_OCTAVE  
  
The highest possible value the octave can have. The pitch of a note will never go above this value. Decrease this to force a darker sound. Increase this to allow higher pitched sound.  

  * note = note + octave * 5

The multiplicator in this variable can be used to change the pitch of the sound. Lower value     means darker sound, higher value means higher pitched sound. Keep in mind that if the multiplicator is increased, the pitch will still not go above the MAX_OCTAVE value.  

  * track.append(midi.NoteOnEvent(tick=0, velocity=70, pitch=note))  

The tick determines when the key is being hit. Increasing this value means it takes longer after a key hit until the next key is being hit.  
The velocity determines how hard the key of a piano for example is being hit, in other words this determines the base volume (max. value: 127).  
The pitch determines the pitch of the note. This is set to the (note = note + octave * 5) variable. Changing said variable therefore changes the pitch.  

  *  track.append(midi.NoteOffEvent(tick=300, pitch=note))  
  
The tick determines how long the key is being pressed. Lowering this value  means notes are being played faster, increasing this value means it takes longer until the next note is being played.  
The pitch is the same as stated before.  

  * track.append(midi.ProgramChangeEvent(data=[0]))  
  
This function determines which instrument is being played. Change the data value to change the instrument.  
Which instrument equals which data parameter can be found here https://en.wikipedia.org/wiki/General_MIDI#Program_change_events  
The value of the instrument is always -1 than shown on wikipedia!

  * Depiction of midi notes   
http://www.electronics.dit.ie/staff/tscarff/Music_technology/midi/midi_note_numbers_for_octaves.htm  

In the link above it is stated that C5 is the middle C note, but sometimes there are alternatives where a software interprets C3 as middle note, shifting the table upwards by 2 octaves.  
This means that additionally, in Line 62, 66, 68, 70, 72 the midi.x_3 can be changed to alter the sound that way as well.  
Currently they are set to C_3, D_3, G_3, F_3, so in the middle of the piano to guarantee a neutral base sound.
