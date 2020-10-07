# GTToolsSharp
A port of gttool from flatz for Gran Turismo 5/6 to C# which allows for endless modding possibilities.

The main motivation for writing this tool was the near unreadability of the main one by flatz, along with extra features.

### Features
* Unpacking from any PS3-era Gran Turismo. (You'll need the keys to decrypt the volumes).
* Unpacking **Patch File Systems** (PDIPFS) from game updates (except 1.06 Bsdiff GT6 files, planned)
* **Packing files for modding** (PDIPFS only, packing an entire 10+gigs GT.VOL is kind of pointless). (Read packing sections)

Files can be extracted for GT.VOL (main build volume) and PDIPFS (update patches).
To unpack/pack certain builds you will need the keys for each one of them. Only the keys for GT5 (EU/US) and GT6 are provided.

## Usage
### Unpacking
Input: `GTToolsSharp unpack -i <input GT.VOL or PDIPFS path> -o <Folder to extract to> (--noprint)`

Examples:
  * Normal Unpack: `GTToolsSharp unpack -i PDIPFS -o PDIPFS_EXTRACTED`

### (Re)Packing (PDIPFS only)
Input: `GTToolsSharp pack -i <PDIPFS> --folder-to-pack <Folder with source files to pack> -o <output of repacked files>`

Examples:
  * Normal Pack: `GTToolsSharp pack -i PDIPFS --folder-to-pack RepackInput -o RepackedFiles`
  * To Delete files: `GTToolsSharp pack -i PDIPFS  --folder-to-pack RepackInput -o RepackedFiles --packremovefiles` (Needs files_to_remove.txt in current folder)
  Where `RepackInput` must contain the same game paths for files, i.e `RepackInput/piece/gt5/course_logo_LL/akasaka.img`
Recommended usage is to **not** to pack to the same input folder. If your input folder is PDIPFS (original), your output folder should not be PDIPFS.

**Make sure to make backups of the files you are reverting. If you get a black screen upon starting the game, revert your files.**

## Advanced Packing Notes (Modders Read)
The Gran Turismo 5 and above uses a file system that allows editing existing files while keeping the actual original files intact. This allows for extremely easy modding an easy revert method.

Important things to know:
1. *Main Volume Header File* - **Always** `PDIPFS/K/4D`
2. Table of Contents file (*TOC*) which contains all the data (each file being an *entry number*) that indicates where an original file is located in the scrambled PDIPFS - location of TOC determined by the master file.
3. The *entry number* is used to determine the PDIPFS scrambled path.

Files packed as new with means that new entries numbers are appended to last one in *TOC* which are the same original paths, but with new entry numbers thus new files are generated, and the older file entries used for these paths are unused. That means that upon packing, new scrambled file names are generated, and do not interfere with any of the other original game files. The only file that is edited is the volume header file which is always located at the *Main Volume Header File*.

The advantage of doing this is that players of your mods only have to backup this one file when applying your mods instead of all the files which would overwrite. To revert, players can simply revert their original `PDIPFS/K/4D` file.

**Note:** Doing this means that your output folder becomes your input folder if you want to use an existing mod as a base mod since it will contain the newer files, table of contents, and header.
* First pack: `GTToolsSharp pack -i PDIPFS --folder-to-pack MyModdedFiles -o PDIPFS_MOD --pack-all-as-new`
* Next packs: `GTToolsSharp pack -i PDIPFS_MOD --folder-to-pack MyModdedFiles -o PDIPFS_MOD2 (for a new folder again)`

Unless you are using a mod as base (and don't have the source files of the mod), keep it one packing only, from your original PDIPFS to your final pack folder.
**As of 2.0.3 this behavior is default**. If you would like your mod to actually overwrite *TOC* files and original files, use `--pack-as-overwrite`.

## Compiling
If you anyhow want to compile this, Visual Studio 2019 Preview & .NET Core 5.0 is required.



