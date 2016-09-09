# ROT13
The ROT13 and ROT47 are fairly easy to implement using the Unix terminal application tr; to encrypt the string "The Quick Brown Fox Jumps Over The Lazy Dog" in ROT13:

  $ # Map upper case A-Z to N-ZA-M and lower case a-z to n-za-m
  $ echo "The Quick Brown Fox Jumps Over The Lazy Dog" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
  Gur Dhvpx Oebja Sbk Whzcf Bire Gur Ynml Qbt

and the same string for ROT47:

  $ echo "The Quick Brown Fox Jumps Over The Lazy Dog" | tr '\!-~' 'P-~\!-O'
  %96 "F:4< qC@H? u@I yF>AD ~G6C %96 {2KJ s@8

and in the Vim text editor, one can ROT13 a selection with the command:[19]

  g?
