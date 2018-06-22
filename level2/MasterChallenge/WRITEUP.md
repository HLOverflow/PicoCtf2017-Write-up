# Master Challenge - Missing Identity [ 100 Pts ]

## Problem

Turns out, some of the files back from Master Challenge 1 were corrupted. Restore this one [file](./file) and find the flag. Update 16:26 EST 1 Apr If you feel that you are close, make a private piazza post with what you have, and an admin will help out. The flag starts with the character z.

## Approach

```
$ xxd file | head
00000000: 5858 5858 5858 0000 0800 2244 7f4a b48b  XXXXXX...."D.J..
00000010: e467 2b90 0000 1c90 0000 0800 0000 666c  .g+...........fl
00000020: 6167 2e70 6e67 0066 4099 bf89 504e 470d  ag.png.f@...PNG.
00000030: 0a1a 0a00 0000 0d49 4844 5200 0002 8100  .......IHDR.....
00000040: 0000 3c08 0200 0000 96fa f76d 0000 8fe3  ..<........m....
00000050: 4944 4154 789c ecfd 59af 6cd9 961e 868d  IDATx...Y.l.....
00000060: 319b d545 1fbb 3f6d b637 336f deae ea96  1..E..?m.73o....
00000070: cc2a 56d9 348b b220 1b86 4c91 8021 d816  .*V.4.. ..L..!..
00000080: 0c43 06fc e627 fd0c c280 fce2 27fa c110  .C...'......'...
00000090: 6c58 76d1 8621 c0b2 4553 022d 362e 56cb  lXv..!..ES.-6.V.
```

From inspecting the file, we see that the first few bytes were overwritten with XXXXXX.
Previously, I tried binwalk and foremost to extract files from known file signatures but were led to a rabbit hole.

The problem to solve is to determine what kind of file is this.

Fortunately, the rabbit hole I went into have a zip file. 

```
masterchallenge/output/zip $ xxd 00000072.zip | head
00000000: 504b 0304 1400 0000 0800 2244 7f4a fe24  PK........"D.J.$
00000010: 8f02 3edd 0000 2add 0000 0f00 0000 6e6f  ..>...*.......no
00000020: 7474 6865 666c 6167 312e 706e 6700 6040  ttheflag1.png.`@
00000030: 9fbf 8950 4e47 0d0a 1a0a 0000 000d 4948  ...PNG........IH
00000040: 4452 0000 03fd 0000 003c 0802 0000 00bb  DR.......<......
00000050: 014d bb00 00dc f149 4441 5478 9cec fd59  .M.....IDATx...Y
00000060: 8c65 5996 2586 ed7d ceb9 e31b 6df6 d9c3  .eY.%..}....m...
00000070: 638e ccc8 c8a1 b2aa ba58 5ddd 2c51 9448  c........X].,Q.H
00000080: 8dfc 1041 482c 50e2 9700 e98b 90a0 0f01  ...AH,P.........
00000090: 029a 1f92 0041 20f4 4d49 a020 b4d0 127f  .....A .MI. ....
```

I realized how similar the next few bytes are, and attempted to use Bless Hex Editor to fix the first 6 bytes saved as recover_file.zip

```
$ unzip recover_file.zip 
Archive:  recover_file.zip
  inflating: flag.png                
  inflating: nottheflag1.png         
  inflating: nottheflag2.png         
  inflating: nottheflag3.png         
  inflating: nottheflag4.png         
  inflating: nottheflag5.png         
  inflating: nottheflag6.png         
  inflating: nottheflag7.png

$ xdg-open recover_file/flag.png
```

The flag is zippidydooda29494995

(Completed)




