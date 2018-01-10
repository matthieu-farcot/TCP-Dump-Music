## Documentation of added code in the python script

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
