# YANG packager

## Introduction

YANG packager is a simple program which is part of the UniConfig distribution. User can finds it in the utils/ directory
after building and unpacking the UniConfig distribution. User can use it by simple shell script called
'convertYangsToUniconfigSchema.sh'. YANG packager is responsible for:

1. validation of user-provided YANG files
2. copying valid YANG files to the user-defined directory
3. informing the user about conversion process

## Usage

Script ```./convertYangsToUniconfigSchema``` contains four arguments. Each one has its own identifier so user can use any order of arguments.
Two arguments are required, namely the path to resources that contain YANG files and the path to the output directory
where user wants to copy all valid YANG files. Other three arguments are optional. First one is the path to the
"default" directory which contains some default YANG files, second one is the path to the "skip-list" and last one is
a "-to-file" flag, which user can use when he wants to write a debug output to file.

* ```-i /path/to/sources``` - required argument. User has two options for where the path can be directed:
    1. to the directory that contains YANG files and other sub-directories with YANG files
    2. to the text-file that contains defined names of directories. These defined directories have to be stored on the same path as text-file.
* ```-o /path/to/output-directory``` - required argument. User can define path where he wants to save valid YANG files. If the output directory exists, it will be replaced by a new one.
* ```-d /path/to/default``` - optional argument. Sometimes some YANG files need additional dependencies that are not provided in source directories. In this case it is possible to use path to the 'default' directory which contains additional YANG files. If there is this missing YANG file, YANG packager will use it.
* ```-s /path/to/skip-list``` - optional argument. User can define YANG file names in text file that he does not want to include in conversion process. This file must only contain module names without revision and .yang suffix.
* ```-to-file``` - optional argument. When user uses this flag, then YANG packager also saves the debug output to a file. This file can be found on a same path as **output-directory**. It will contain suffix '-info' in its name. If the output directory is called 'output-directory', then the file will be called 'output-directory-info'.

!!!
Bash script ```./convertYangsToUniconfigSchema``` also includes simple help facility. There are two options how to show the help text:
 1. ```./convertYangsToUniconfigSchema -h```
 2. ```./convertYangsToUniconfigSchema --help```
!!!

!!!
The user is responsible for the validity of YANG files in the default directory. These files are not checked by YANG package.
!!!

!!!
If compilation process detected some invalid YANG files then output directory will not be created. 
In this case, user has to fix invalid YANG files or use a combination of "-d" and "-s" arguments.
!!!

## Example use-case

### Basic usage 1

This is basic usage of the script where only mandatory arguments are used. In this case, there is a directory with YANG files used as source. All files in source directory are valid YANG files.
Open a terminal, go to the ../utils directory and run command:

```console
./convertYangsToUniconfigSchema -i /path/to/source-directory -o /path/to/output
```

```console Output:
--------------- SOURCE DIRECTORY ---------------
/path/to/source-directory
Number of source files:   593
Number of valid files:    593
Number of invalid files:  0
Number of non-yang files: 0
```

### Basic usage 2

This is basic usage of the script where only mandatory arguments are used. In this case, there is directory with YANG files used as source. Source directory also contains one invalid YANG file with missing import.
Open a terminal, go to the ../utils directory and run command:

```console
./convertYangsToUniconfigSchema -i /path/to/source-directory -o /path/to/output
```

```console Output:
--------------- SOURCE DIRECTORY ---------------
/path/to/source-directory
---------------
Invalid files:
---------------
name:  cisco-xr-openconfig-mpls-deviations@2016-05-16.yang
path:  /path/to/source-directory/cisco-xr-openconfig-mpls-deviations@2016-05-16.yang
error: Missing imports: openconfig-mpls
Number of source files:   594
Number of valid files:    593
Number of invalid files:  1
Number of non-yang files: 0

Yang packager failed because:
 - source directories contain some invalid files. Because of this reason nothing will be copied to destination directories
```

### Basic usage 3

This is basic usage of the script where only mandatory arguments are used. In this case, there is directory with YANG files used as source. Source directory also contains one non-yang file.
Open a terminal, go to the ../utils directory and run command:

```console
./convertYangsToUniconfigSchema -i /path/to/source-directory -o /path/to/output
```
```console Output:
--------------- SOURCE DIRECTORY ---------------
/path/to/source-directory
---------------
Non-yang files:
---------------
name:  test.sh
path:  /path/to/source-directory/test.sh
error: Non Yang file
Number of source files:   594
Number of valid files:    593
Number of invalid files:  0
Number of non-yang files: 1
```

### Usage with default directory

This is usage with path to default directory that contains one YANG file openconfig-mpls. Source directory also contains one invalid YANG file 'cisco-xr-openconfig-mpls-deviations.yang' with missing import 'openconfig-mpls'. This missing import is loaded from default directory.
Open a terminal, go to the ../utils directory and run command:

```console
./convertYangsToUniconfigSchema -i /path/to/source-directory -o /path/to/output -d /path/to/default
```

```console Output:
--------------- SOURCE DIRECTORY ---------------
/path/to/source-directory
Number of source files:   594
Number of valid files:    594
Number of invalid files:  0
Number of non-yang files: 0
Number of source files added from default source directory: 1

List of added sources: openconfig-mpls@2015-11-05.yang
```

### Usage with skip-list

This is usage with path to skip-list text file that contains one YANG file name cisco-xr-openconfig-mpls-deviations.
This YANG file will not be included in the conversion process.
Open a terminal, go to the ../utils directory and run command:

```console
./convertYangsToUniconfigSchema -i /path/to/source-directory -o /path/to/output -s /path/to/skip-list
```

```console Content of the skip-list
cat /path/to/skip-list
cisco-xr-openconfig-mpls-deviations
```

```console Output:
--------------- SOURCE DIRECTORY ---------------
/path/to/source-directory
Number of source files:   593
Number of valid files:    593
Number of invalid files:  0
Number of non-yang files: 0
```

### Usage with text-file as a source

In this example a path to text-file with defined names of source directories is used.

Open a terminal, go to the ../utils directory and run command:

```console
./convertYangsToUniconfigSchema -i /path/to/text-file -o /path/to/output
```

```console Content of the text-file
cat /path/to/text-file
directory-1
directory-2
```

```console Output:
--------------- SOURCE DIRECTORY ---------------
/path/to/directory-1
Number of source files:   593
Number of valid files:    593
Number of invalid files:  0
Number of non-yang files: 0
--------------- SOURCE DIRECTORY ---------------
/path/to/directory-2
Number of source files:   515
Number of valid files:    515
Number of invalid files:  0
Number of non-yang files: 0
```

### Usage with -to-file flag

This is usage where output is also printed to file. User can find output information file on the path **/path/to/output-info**.

Open a terminal, go to the ../utils directory and run command:

```console
./convertYangsToUniconfigSchema -i /path/to/source-directory -o /path/to/output -d /path/to/default
```

```console Output:
--------------- SOURCE DIRECTORY ---------------
/path/to/source-directory
Number of source files:   593
Number of valid files:    593
Number of invalid files:  0
Number of non-yang files: 0
```

### Usage with text-file as a source and -to-file flag

In this example a path to text-file with defined names of source directories is used and also flag for print outputs to files.
User can find output information files on paths /path/to/output/directory-1-info and /path/to/output/directory-2-info

Open a terminal, go to the ../utils and run command:

```console
./convertYangsToUniconfigSchema -i /path/to/text-file -o /path/to/output -to-file
```

Content of text-file

```console
cat /path/to/text-file
directory-1
directory-2
```

```console Output:
--------------- SOURCE DIRECTORY ---------------
/path/to/directory-1
Number of source files:   593
Number of valid files:    593
Number of invalid files:  0
Number of non-yang files: 0
--------------- SOURCE DIRECTORY ---------------
/path/to/directory-2
Number of source files:   515
Number of valid files:    515
Number of invalid files:  0
Number of non-yang files: 0
```

### Error - source directory does not exist

User-defined source directory does not exist.

Open a terminal, go to the ../utils directory and run command:

```console
./convertYangsToUniconfigSchema -i /path/to/source-directory -o /path/to/output
```

```console Output:
YANG packager failed because:
 - source '/path/to/source-directory' does not exist
```

### Error - source directory is empty

User-defined source directory is empty.
Open a terminal, go to the ../utils directory and run command:

```console
./convertYangsToUniconfigSchema -i /path/to/source-directory -o /path/to/output
```

```console Output:
YANG packager failed because:
 - source '/path/to/source-directory' is empty
```

### Error - sources defined in text-file

One directory defined in the text-file is empty and other one does not exist.

Open a terminal, go to the ../utils and run command:

```console
./convertYangsToUniconfigSchema -i /path/to/text-file -o /path/to/output
```

Content of text-file

```console
cat /path/to/text-file
directory-1
directory-2
directory-3
```

```console
YANG packager failed because:
 - source '/path/to/directory-1' is empty
 - source '/path/to/directory-3' does not exist
```
