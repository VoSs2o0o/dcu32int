# DCU32INT - Delphi Compiled Units Parser
The utility DCU32INT parses *.dcu file and converts it into a close to Pascal form.

url: https://github.com/VoSs2o0o/dcu32int
original author: http://hmelnov.icc.ru/DCU/index.eng.html
forked from: https://github.com/Hunter342/dcu32int

## Versions supported

Delphi 2-8, 2005-2010, XE-XE8, 10.x and 11.x till 11.3 are supported at moment

It will be developed with Delphi 11.3 (compatible down to 10.0).
Older Versions will be not actively tested. 
   
------------------------------------------------------------------------------------------------------
## Notes:

If you'd like to try parsing a file with a version that I haven't tested, please share your analysis with me. 
I will update the information based on the results of your analysis of the files.

## Usage:

```
DCU32INT <Source file> <Flags> [<Destination file>]
Source file - DCU(DPU,DCUIL) or Package (DCP,DCPIL) file
Destination file may contain * to be replaced by unit name or name and extension
Destination file = "-" => write to stdout.
Flags (start with "/" or "-"):
 -S<show flag>* - Show flags (-S - show all), default: (+) - on, (-) - off
    A(-) - show Address table
    C(-) - don't resolve Constant values
    D(-) - show Data block
    d(-) - show dot types
    F(-) - show Fixups
    H(+) - show Heuristic strings
    I(+) - show Imported names
    L(-) - show table of Local variables
    M(-) - don't resolve class Methods
    O(-) - show file Offsets
    S(-) - show Self arguments of methods and 2nd call flags of `structors
    T(-) - show Type table
    U(-) - show Units of imported names
    V(-) - show auxiliary Values
    v(-) - show VMT
 -O<option>* - code generation options, default: (+) - on, (-) - off
    V(-) - typed constants as Variables
    S(-) - check unit Stamps
 -I - interface part only
 -U<paths> - Unit directories, * means autodetect by unit version
 -LF[<File name>] - Libraries Config.ini file name (used by -U with *)
 -LD - use debug libraries (used by -U with *)
 -P<paths> - Pascal source directories (just "-P" means: "seek for *.pas in
    the unit directory"). Without this parameter src lines won't be reported
 -R<Alias>=<unit>[;<Alias>=<unit>]* - set unit aliases
 -N<Prefix> - No Name Prefix ("%" - Scope char)
 -F<FMT> - output format (T - text (default), H-HTML)
 -D<Prefix> - Dot Name Prefix ("%" - Scope char)
 -Q<Query flag> - Query additional information.
    F(-) - class fields
    V(-) - class virtual methods
 -A<Mode> - disAssembler mode
    S(+) - simple Sequential (all memory is a sequence of ops)
    C(-) - Control flow
 -X - Extract DCU files from DCP file
```

The Scope char symbol will be replaced in the name by "T" for types "C" for
constants and so on (see source for details).

In general, there are two main ways to run the program:
- without the -S switch - to produce the most close to the original Pascal 
  source output without superfluous details;
- with the -S switch to see a lot of additional information, which reflects 
  the internal structure of the DCU file, e.g. the values of some fields of
  unknown purpose (You can try to guess what they mean), the data structures
  representing the VMT of classes, RTTI of data types or the table of 
  addresses.
Of course, You can always select only a subset of additional information using 
the -S<flags>.

# License
https://www.apache.org/licenses/LICENSE-2.0

# original License
                             IMPORTANT NOTE:

This software is provided 'as-is', without any expressed or implied warranty.
In no event will the author be held liable for any damages arising from the
use of this software.
Permission is granted to anyone to use this software for any purpose,
including commercial applications, and to alter it and redistribute it
freely, subject to the following restrictions:
1. The origin of this software must not be misrepresented, you must not
   claim that you wrote the original software.
2. Altered source versions must be plainly marked as such, and must not
   be misrepresented as being the original software.
3. This notice may not be removed or altered from any source
   distribution.

# History Changes from the version 1.10.0

## Version 1.28.1 (2023, 13, Oct)
- old readme readded as readme_old.txt
- fix the $9C Tag - Error, as suggested here: https://github.com/Hunter342/dcu32int/issues/2

## Version 1.28.0 (Jun 19, 2023) by Hunter342:
- New types $08, $16 added to ConstAddInfo
- DCUFromDCP broken

## Version 1.27.1 (Sep 19, 2022) by Hunter342:
- DCUFromDCP fixes

## Version 1.27.0 (Sep 19, 2022) by Hunter342:
- Extract DCP: You can now extract DCU(_Delphi Compiled Unit_) files from 
DCP(_Delphi Compiled Package_) using the -X option. (**Example: dcu32int -X "mydcp.dcp"**)

## Version 1.26.2 (Aug 21, 2022) by Hunter342:
- small fixes

## Version 1.26.1 by hmelnov:

1. The units of Delphi 10.3 Rio are supported now, the Linux platform support 
  was not checked, because the Community Edition was used. 
2. The complete check of inline code support has shown some specification
  errors, the errors were fixed.
3. The XML-based disassembler can display information about instruction 
  arguments, when the flag -ADX is turned on in the command line.

## Version 1.25.1 by hmelnov:

1. The units of Delphi 10.2 Tokyo are supported now, including that for the 
  Linux platform. 
2. The actual code for a Linux unit is also contained in the 
  corresponding .o file (the same situation as that for the iOS and Android
  device units). So *.o (COFF object file format) processing is required to 
  completly support Linux units in DCU32INT. This capability is not 
  implemented yet.
3. Th DCU32INT code can now be compiled in 64-bit mode.
4. Inline code for constant expressions was detected and supported.

## Version 1.24.1 by hmelnov:

1. The units of Delphi 10.1 Berlin are supported now. 
2. The representation of inline bytecode has been explored and 
  discovered (see the unit Inline.pas for more details). The results of
  this research allow to obtain the close to the original code,
  which is much more readable, than the assembly code.
  The main differences between decompiled from inline and original code are: 
  representation of method calls in the form of procedure call 
  (i.e. Class.Method(Self,...) instead of Self.Method(...)); 
  some system procedure-like statements (e.g. Write) are implemented 
  using other functions; such operators as for ... in or anonymous 
  functions are implemented using rather complex code (creation of aux objects,
  guarding their destruction by try ... finally, and so on).

  The decompiled inline bytecode is placed inside the conditional defines
  {$IFDEF UseInlineCode}<Inline decompiled>{$ELSE}<Disassembled code>{$ENDIF}.

  The main advantage of decompiling inline bytecode is noticeable for templates:
  the procedures and methods of templates don`t have 80x86 (or ARM) code at all, 
  so before understanding the inline code their bodies were empty.

  The decompiler of inline code has passed the "parse all libs" test and also 
  was checked on a specially written test code. But it still has some gaps in 
  the codes of inline instructions (the codes, which were never encountered). 
  So, if the decompiler fails to process your DCUs, please, send them to me
  (with the source, if available)!

3. It is now possible to create a library configuration file for DCU32INT,
  which allows to automatically detect the location of system libraries for 
  the DCU being parsed. To use the configuration add the parameter
  -LF<Path to Config>. For example, I use the following commands in the menu
  of FAR manager to parse DCUs of any kind

:  DCU Parse
{
:  Debug
    C:\PRG\DCU32INT\dcu32int.exe -AD -S "!.!" -LFC:\PRG\DCU32INT\Config.ini
:  Final
    C:\PRG\DCU32INT\dcu32int.exe -AD "!.!" -LFC:\PRG\DCU32INT\Config.ini
}
  
  The file Config.ini on my computer is available in the archive, edit it 
  according to the available on your computer Delphi versions, but don`t 
  change the key names, just their value. The key CFG.LIBROOT specifies the
  root directory for all the relative (not absolute) library paths.

## Version 1.23.1 by hmelnov:

1. The units of Delphi 10 Seattle are supported now. 
2. The code for reading DCU magic was rewritten in a more compact way using
  regularity of the magic values.
3. An alternative disassembler for 80x86 bytecode has been implemented. 
  It is based upon the XML specificateon of the 80x86 bytecode from http://ref.x86asm.net/.
  To turn the use of the XML-based disassembler on use the XMLx86 conditional 
  define. This define is turned on in the project options now, because the new 
  disassembler can parse more opcodes, and, in particular, the XMM commands, 
  which are used in WIN64 for implementation of floating point arithmetics. 
  The source code of XML-based disassembler is located in the .\80x86 folder.
  The XML-based disassembler doesn`t read and parse the XML each time, when 
  the program starts. Instead, it uses the constant tables, which represent 
  the information from the XML file (see the unit .\80x86\x86Op.pas). 

## Version 1.22.1 by hmelnov:

1. The XE8 units are supported now. 
2. Some version codes in DCU32INT (including that in DCU32INT version number)
  were shifted by 1 to make them compatible with Delphi product versions.
3. The control flow analysis now uses code sections dominance relations to 
  compute their indentation, which often corresponds to operator embedding.
4. The structured exception handling data structures of WIN64 are now used 
  to detect and mark the exception handling code, which is unreachable
  by the regular control flow. See the file Win64SEH.pas for more details.

## Version 1.20.1 by hmelnov:

1. The XE6 and XE7/AppMethod units are supported now (the trial version of 
  AppMethod, which I have got, corresponds to XE7, but I`m not sure whether 
  it was always so). The main format change is addition of drNextOverload 
  records after overloaded procedures.
2. Now it is possible to parse packages (*.dcp and *.dcpil) and units 
  from packages. To parse package just give its file name in the program 
  parameter instead of unit file name. To parse unit from package append 
  '@' and unit name with extension to the package name. For example:
    .\AppMethod\15.0\lib\win32\release\bindcomp.dcp@Data.Bind.dcu
  To include some package to the unit search path add its file path to the 
  semicolon-separated directory list in the -U parameter. To include all 
  the packages in some directory <DIR> to the search path add <DIR>\*.dcp 
  or <DIR>\*.dcpil to the directory list. For example the parameter:
    "-U.\D2005\lib;.\D2005\lib\*.dcpil"
  allows to seek *.dcuil files in the lib directory and, if the file is 
  missing, in all the packages of this directory. I ignored packages for 
  a long while, because I thought that they won`t contain much information, 
  but I was wrong. In fact a DCU in package contains the complete information 
  of the interface part of the corresponding PAS file and even some information 
  from the implementation part (e.g. local declarations and even machine 
  instructions of some procedures are present).
3. The package loading process has been split into two phases: loading
  the package header with the list of the names of its units and loading 
  the rest of the package file, when some unit from the package is 
  being fetched. It allows to accelerate processing when the DCU search 
  path contains a lot of packages, but only some of them are indeed 
  required. The process may be fine tuned by the following variables of 
  the DCP unit:
    TwoPhaseDCPLoad: Boolean = true; - allows to turn off the two phase 
      loading process
    TwoPhaseDCPSizeLimit: Cardinal = 65536; - a heuristic limit on the package 
      size. Only the packages of size larger than this value will be loaded 
      in two phases.
    TwoPhaseInitSize: Cardinal = $800; - the size of the initial part of the 
      DCP file that will be loaded first. The size should be less than 
      TwoPhaseDCPSizeLimit and greater than $24 (the largest package header 
      record size). If the package contents table doesn`t fit into the
      limit, then an additional read operation will be required to load the
      complete unit list.
4. The unit stamp check has been turned off, because for many units the 
  values in the unit header and in the corresponding import list record 
  of a using unit differ. To turn on the check use the -OS flag.

## Version 1.19.1 by hmelnov:

1. Units of Delphi XE6 are supported. The main format change is 
  explicit representation of attributes in drConstAddInfo records.
2. The attributes of declarations (those notes in square brackets like 
  [Weak]) were introduced yet in D2010, but since then the attribute 
  information was written directly into the extended in D2010 RTTI. 
  In XE6 the attributes has got an explicit representation in the 
  drConstAddInfo records, and now DCU32INT decodes the information 
  (but only for XE6 up units). It could be possible to extract the 
  information from RTTI for D2010-XE5, but this capability is not 
  supported yet in DCU32INT.
3. To represent in memory some information extracted from TConstAddInfo 
  (like attributes in XE6 up, notes in deprecated, and so on) an 
  abstract class TDeclModifier and its concrete descendants have been
  added to DCURecs. Now TNameDecl contains the list Modifiers, which may
  contain an additional information specified for the declaration.
4. The declaration lists are now considered as lists of arbitrary TDCURec 
  descendants and not that of TNameDecl as it was in the prior versions. 
  It allows to insert into the list DCU records of any type if required.
5. An old memory leak issue has been fixed. I always somehow supposed that 
  TDCURec constructor registers automatically the record in CurUnit 
  (perhaps I was going to do it this way some time ago), but it was wrong. 
  Now all the records, that were not placed in the current declaration list, 
  will be placed in the FOtherRecords list and freed with unit.
6. A problem with ExportDecls and ExportTypes properties of TUnit has been 
  fixed. When filling the FExportNames list, which is used by these properties, 
  in the SetExportNames method, the declaration visibility in interface 
  section was considered as a criteria of declaration selection. As a result 
  the class members (methods, etc) were skipped. Now the only sign
  of declaration export is presence of the global visibility flag $40.
7. In DasmCF.pas it is now possible to extend the TCmdSeq class if required
8. The MSIL procedure header with exception handler table support has 
  been added

## Version 1.18.1 by hmelnov:
1. Units of Delphi XE5 are supported now. No file format changes since XE4
  besides from that in magic numbers were observed.
2. The actual code for an Android device unit is also contained in the 
  corresponding .o file (the same situation as that for the iOS device units).
  So *.o (ELF object file format) processing is required to completly 
  support Android device units in DCU32INT. This capability is not 
  implemented yet.

## Version 1.17.1 by hmelnov:
1. Units of Delphi XE4 are supported now.
2. The actual code for an iOS device unit is contained in the corresponding
  .o file (which, I believe, is produced by LLVM back end AFTER creation of 
  the corresponding .dcu file by Delphi front end, so the iOS device 
  DCU can`t contain addresses in .o memory, and the correspondence between 
  the DCU records and .o sections is mantained by mangled section names only).
  So *.o (Mach-o object file format) processing is required to completly 
  support iOS device units in DCU32INT. This capability is not implemented yet.
3. The -Q command line switch has been added. It allows to output the tables 
  of class fields (with the corresponding object memory offsets) and virtual 
  methods (with the corresponding VMT offsets). The tables may be used to 
  alleviate manual disassembled code analysis, when static disassembler can`t
  determine the data types of a register contents.
4. The UnicodeString constants are shown correctly now.

## Version 1.16.1 by hmelnov:
1. Units of Delphi XE3 are supported now.
2. The source code od DCU32INT has been successfully ported from D3 to XE2
  (i.e. Unicode Delphi versions can now be used to compile it).
3. More correct processing of embedded lists 
  (tags drEmbeddedProcStart/drEmbeddedProcEnd) - 
  thanx to Crypto for the message about this bug.
4. The source files references are now decoded by source file index search 
  instead of source file ordinal numbers.

## Version 1.15.1 by hmelnov:
1. Units of Delphi XE2 are supported now.
2. Intel 64 disassembler has been added, 
  and the units for WIN64 and OSX32 are supported now.
3. PName was changed from PShortString to the data structure TNameRec 
  describing long strings (longer than 255 bytes), which can happen as 
  a result of mangling or template instantiation starting from D2009.
4. Aux fields for properties of the kind 
  property X: Integer read FP.X
  detection and replacing by the corresponding qualifiers.
5. Added library autodetection flag -U*, which is turned on by default 
  (use -U or -U with some different path to turn it off). This flag allows
  to automatically detect the library path for a Delphi version installed
  on the computer where dcu32int runs.

## Version 1.14.1 by hmelnov:

1. Units of Delphi XE are supported now.
2. XE DCUs store information about data types defined inside procedures
  separately from the procedure declaration. I'll call this situation 
  "orphaned types problem". To fix the problem somehow the method
  TDCURec.EnumUsedTypes has been implemented for the most part of objects,
  which may refer to data types (some elements of class declarations were
  skipped, cause the classes can't be local). So, once a local data type 
  was used, e.g. to declare a local variable, this information allows to 
  find the place, where the data type belong. But, when a local data type 
  was used, e.g.  for typecasting only, no evidence for its usage will 
  remain in the DCU. The orphaned data types which were not bound to their 
  procedures by this process, are placed by DCU32INT in the list of global 
  declarations.

## Version 1.13.1 by hmelnov:

1. Units of Delphi 2009 and 2010 are supported now.
2. Processing of some units of older versions was fixed by taking into 
  account the fact that some structures may sometimes contain forward 
  references to addresses, which are not defined yet. The drProcAddInfo tag 
  will be used then to specify the correct index of the referenced address.

## Version 1.10.2 by hmelnov:

1. Added HTML output (use the -FH flag to turn it on) with syntax markup and
  hyperlinks. Should be improved later (not all definitions (e.g. procedure
  arguments) are marked by <A NAME=...> tags yet).
2. Improved compileability of the code generation and fixed some errors (thanx
  to Josef Grosch):
  - class member visibility for versions 8 up;
  - corrected overload and inline procedures modifier for versions 9 up;
  - class member visibility for interfaces is now commented out or not shown;
  - resourcestring declarations are shown now according to correct Pascal syntax;
  - class field declarations after method or property declarations with the
    same visibility are now prepended with additional visibility marker;
  - DecimalSeparator is now '.' .

## Version 1.10.1 by hmelnov:

1. Added displaying of possible inline string constants (use -SH
  to turn it off).
2. The Self arguments of methods and 2nd call flags of constructors
  and destructors are hidden now. To show them as before use -SS option.
3. Auxiliary fake procedures, which hold the values of huge string
  typed constants are marked now by the JustData flag and not disassembled.
