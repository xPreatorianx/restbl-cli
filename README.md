# RESTBL-CLI
RESTBL-CLI is a simple rewrite of dt12345's tool [(RESTBL)](https://github.com/dt-12345/totktools) to work with command-line arguments instead of a GUI with buttons.

This is a tool for working with and merging RESTBL files in *Tears of the Kingdom*. RESTBL files are used by the game's resource system to decide how much memory it should allocate for each file. Each entry in a RESTBL file contains a CRC32 hash of the corresponding file's path and its allocation size. The allocated size as listed in the RESTBL is not exactly equal to the size of the file, it is slightly larger.

When developing or using mods, the RESTBL oftens becomes an issue as changes to file sizes may lead to the existing RESTBL entry becoming too small. This will result in the game crashing when it attempts to load in said resource. As a result, many mods come with edited RESTBL files, tailored for that specific mod. The issue arises when you have multiple mods all requiring RESTBL edits. This tool aims to solve that issue by automating the process without the need for external changelog files (such as YAML patches or .rcl files).

## Usage
To use the tool, simply download `restbl-cli.exe` and run it from the command line using `restbl -h` to get all available options. There are four options to choose from: merge-mods, merge-restbl, generate-changelog, apply-patches

### merge-mods
This option will analyze the provided mod directories and automatically generate an edited RESTBL file.

Using the `--compress` option will compress the generated RESTBL file with Zstd compression.

Using the `--use-existing-restbl` option will search each mod for an existing RESTBL file. If the file exists, that file will be used for that mod instead of analyzing the directory.

Using the `--delete-existing-restbl` option will automatically delete any existing RESTBL files present in the mod(s) during analysis to prevent any potential file conflicts. This option is compatible with the `--use-existing-restbl` option.

Using the `--use-checksums` option will compare each file in the mod with the unmodified file by comparing a SHA-256 hash of the file. This avoids creating unnecessary RESTBL entries for unmodified files that may be present in the mod.

When using `merge-mods`, the argument `--mod-path C:\Path\to\mods` is required. This should be the folder so that the path `[selected_path]/[mod_name]/romfs` exists.

**Example:**
```
 Selected Folder/
    ├── Mod 1/
    │   └── romfs
    ├── Mod 2/
    │   └── romfs
    └── Mod 3/
        └── romfs
```
> restbl-cli.exe --action merge-mods --use-checksums --compress --mod-path "C:\Users\username\AppData\Roaming\Ryujinx\mods\contents\0100f2c0115b6000"

> restbl-cli.exe -a merge-mods  -cs -c -m "C:\Users\username\AppData\Roaming\Ryujinx\mods\contents\0100f2c0115b6000"

### - merge-restbl
This option will create a merged RESTBL file from the two provided RESTBL files. Similar to the previous option, using `--compress` will compress the generated RESTBL file with Zstd compression.

> restbl-cli.exe --action merge-restbl  --compress --restbl-path0 "C:\path\to\file1\ResourceSizeTable.Product.120.rsizetable.zs" --restbl-path1 "C:\path\to\file2\ResourceSizeTable.Product.120.rsizetable.zs"

> restbl-cli.exe -a merge-restbl  -c -r0 "C:\path\to\file1\ResourceSizeTable.Product.120.rsizetable.zs" -r1 "C:\path\to\file2\ResourceSizeTable.Product.120.rsizetable.zs"


### - generate-changelog
This option will generate a changelog in the format of your choice from the selected RESTBL file.

> restbl-cli.exe --action generate-changelog --log-restbl-path "C:\path\to\file\ResourceSizeTable.Product.112.rsizetable.zs" --format rcl

> restbl-cli.exe -a generate-changelog -l "C:\path\to\file\ResourceSizeTable.Product.112.rsizetable.zs" -f rcl


### - apply-patches
This option will apply all RCL/YAML patches in a folder to the selected RESTBL file.

> restbl-cli.exe --action apply-patches  --compress --patch-restbl "C:\path\to\file\ResourceSizeTable.Product.121.rsizetable.zs" --patches-path "C:\path\to\folder\containing\json_rcl_yaml_patches"

> restbl-cli.exe -a apply-patches  -c -p "C:\path\to\file\ResourceSizeTable.Product.121.rsizetable.zs" -pp "C:\path\to\folder\containing\json_rcl_yaml_patches"


## Help
```
RESTBL Tool

options:
  -h, --help            show this help message and exit
  -a {merge-mods,merge-restbl,generate-changelog,apply-patches},
  --action {merge-mods,merge-restbl,generate-changelog,apply-patches}
                        Action to perform
  -c, --compress        Compress the output
  -v, --verbose         Print the list of edited files from mods

merge-mods:
  -m MOD_PATH, --mod-path MOD_PATH
                        (Mandatory) Path to the mod directory
  -ver VERSION, --version VERSION
                        (Optional) TotK version - default: 121
  -u, --use-existing-restbl
                        (Optional) Use existing RESTBL
  -r RESTBL_PATH, --restbl-path RESTBL_PATH
                        (Optional) Path to the RESTBL file to use
  -del, --delete-existing-restbl
                        (Optional) Delete existing RESTBL
  -cs, --use-checksums  [Recommended] Use checksums

merge-restbl:
  -r0 RESTBL_PATH0, --restbl-path0 RESTBL_PATH0
                        (Mandatory) Path to the first RESTBL file to merge
  -r1 RESTBL_PATH1, --restbl-path1 RESTBL_PATH1
                        (Mandatory) Path to the second RESTBL file to merge

generate-changelog:
  -l LOG_RESTBL_PATH, --log-restbl-path LOG_RESTBL_PATH
                        (Mandatory) Path to the RESTBL file for generating changelog
  -f {json,rcl,yaml}, --format {json,rcl,yaml}
                        (Mandatory) Format of the changelog

apply-patches:
  -p PATCH_RESTBL, --patch-restbl PATCH_RESTBL
                        (Mandatory) Path to the RESTBL file to patch
  -pp PATCHES_PATH, --patches-path PATCHES_PATH
                        (Mandatory) Path to the folder containing patches (rcl, yaml, json)
```
