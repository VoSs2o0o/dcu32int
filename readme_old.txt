The DCU32INT utility by Alexei Hmelnov.
Version 1.26.1

----------------------------------------------------------------------------
E-Mail: alex@icc.ru
http://hmelnov.icc.ru/DCU/
----------------------------------------------------------------------------

Purpose.
--------

Parse Delphi 2.0-8.0, 
2005-2006/Turbo Delphi (.net and WIN32), 
2007-2010 (WIN32), XE (WIN32), XE2-XE3 (WIN32,WIN64,OSX32), 
XE4 (WIN32,WIN64,OSX32,iOS simulator, iOS device (no code)), 
XE5-XE7/AppMethod (WIN32,WIN64,OSX32,iOS simulator,
  iOS device (no code), Android (no code)), 
XE8, 10 Seattle, 10.1 Berlin (WIN32,WIN64,OSX32,iOS simulator,
  iOS device 32/64 (no code),Android (no code)), 
10.2 Tokyo (WIN32,WIN64,OSX32,iOS simulator, 
  iOS device 32/64 (no code),Android (no code),Linux (no code)), 
10.3 Rio (WIN32,WIN64,OSX32,iOS simulator, 
  iOS device 32/64 (no code),Android (no code),Linux (may be - not checked,no code)), 
Kylix 1.0-3.0 units (DCU) 
and convert their information into the close to 
Pascal form.

DCU32INT stands for DCU32 INTerface, because this program can't extract
the complete Pascal source, but the extracted unit interface is almost
correct (see Compiler Information Loss Limitations section for exceptions).

This program is a by-product of the FlexT project (see
http://hmelnov.icc.ru/FlexT/ for details), but
I have done my best to make it useful.


Changes from the version 1.0.
-----------------------------

Version 1.26.1:

1. The units of Delphi 10.3 Rio are supported now, the Linux platform support 
  was not checked, because the Community Edition was used. 
2. The complete check of inline code support has shown some specification
  errors, the errors were fixed.
3. The XML-based disassembler can display information about instruction 
  arguments, when the flag -ADX is turned on in the command line.

Version 1.25.1:

1. The units of Delphi 10.2 Tokyo are supported now, including that for the 
  Linux platform. 
2. The actual code for a Linux unit is also contained in the 
  corresponding .o file (the same situation as that for the iOS and Android
  device units). So *.o (COFF object file format) processing is required to 
  completly support Linux units in DCU32INT. This capability is not 
  implemented yet.
3. Th DCU32INT code can now be compiled in 64-bit mode.
4. Inline code for constant expressions was detected and supported.

Version 1.24.1:

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

Version 1.23.1:

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

Version 1.22.1:

1. The XE8 units are supported now. 
2. Some version codes in DCU32INT (including that in DCU32INT version number)
  were shifted by 1 to make them compatible with Delphi product versions.
3. The control flow analysis now uses code sections dominance relations to 
  compute their indentation, which often corresponds to operator embedding.
4. The structured exception handling data structures of WIN64 are now used 
  to detect and mark the exception handling code, which is unreachable
  by the regular control flow. See the file Win64SEH.pas for more details.

Version 1.20.1:

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

Version 1.19.1:

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

Version 1.18.1:
1. Units of Delphi XE5 are supported now. No file format changes since XE4
  besides from that in magic numbers were observed.
2. The actual code for an Android device unit is also contained in the 
  corresponding .o file (the same situation as that for the iOS device units).
  So *.o (ELF object file format) processing is required to completly 
  support Android device units in DCU32INT. This capability is not 
  implemented yet.

Version 1.17.1:
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

Version 1.16.1:
1. Units of Delphi XE3 are supported now.
2. The source code od DCU32INT has been successfully ported from D3 to XE2
  (i.e. Unicode Delphi versions can now be used to compile it).
3. More correct processing of embedded lists 
  (tags drEmbeddedProcStart/drEmbeddedProcEnd) - 
  thanx to Crypto for the message about this bug.
4. The source files references are now decoded by source file index search 
  instead of source file ordinal numbers.

Version 1.15.1:
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

Version 1.14.1:

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

Version 1.13.1:

1. Units of Delphi 2009 and 2010 are supported now.
2. Processing of some units of older versions was fixed by taking into 
  account the fact that some structures may sometimes contain forward 
  references to addresses, which are not defined yet. The drProcAddInfo tag 
  will be used then to specify the correct index of the referenced address.

Version 1.10.2:

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

Version 1.10.1:

1. Added displaying of possible inline string constants (use -SH
  to turn it off).
2. The Self arguments of methods and 2nd call flags of constructors
  and destructors are hidden now. To show them as before use -SS option.
3. Auxiliary fake procedures, which hold the values of huge string
  typed constants are marked now by the JustData flag and not disassembled.

Version 1.10.0:

1. Units of Delphi 2006 (WIN32) were parsed successfully.

Version 1.9.0:

1. Units of Delphi 2005 (WIN32, .net) were parsed successfully.

Version 1.8.0:

1. The generic disassembler was implemented. It is now possible to 
  register a new disassemblers using the call

  SetDisassembler(ReadCommand, ShowCommand,CheckCommandRefs)

2. The MSIL (MicroSoft Intermediate Layer) disassembler for .net code
  was implemented.

3. Units of Delphi 8.0 were parsed successfully.

Version 1.7.4:

1. Some control flow analysis was implemented for disassembler
  (the -AC program option).

Version 1.7.3:

1. Units of Delphi 7.0 were parsed successfully.

Version 1.7.2:

1. Units of open Kylix 3.0 were parsed successfully.

Version 1.7.1:

1. Units of open Kylix 3.0 were parsed successfully.

Version 1.7.0:

1. Some units of trial Delphi 7.0 were parsed successfully.

Version 1.6.4:

1. A problem was fixed with resourcestring sections inside procedures.

Version 1.6.3:

1. Some tricky Kylix header fields prevented from processing some files.
  This problem was fixed, but only for the examples of Kylix DCUs,
  which I have. Please, send me DCUs, which still can't be parsed.

Version 1.6.2:

1. Kylix 2.0 units are supported. In fact, only the file signature
  change was detected.

Version 1.6.1:

1. The DCU32INT sources can now be compiled under Kylix.
2. User units compiled under Kylix have another header structure,
  than that of Kylix LIB units, on which DCU32INT was originally
  tested. The program was fixed to take it into account.

Version 1.6.:

1. Delphi 6.0 and Kylix 1.0 units are supported now.
  Note, this feature is new and it was tested almost only on the
  .\LIB\*.dcu files (see Validity), so bug reports are welcome
  with the units, which were not parsed correctly, applied.
  Please send them to alex@icc.ru.
2. When analyzing Delphi 6 and Kylix DCU format, the data types
  of some fields were clarified. In particular, some byte fields
  become indices, because additional bits of their values were used.
  I believe, that the fields were indices in all the previous Delphi
  versions too, but I had not enough information to detect it.
  So I hope, that the new DCU specification become more precise.
  But, if somebody will encounter units of previous Delphi
  versions, which DCU32INT can't parse now, please, send them to me.
3. Some additional tables in the tail of DCU, which were ignored
  by version 1.0, are processed now. In particular, the program reports
  line numbers in the disassembled code, if the line numbers information is
  present.

Usage:
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


Validity.
---------

The DCU32INT utility has passed successfully the "parse all .\LIB" test 
for all the supported by it Delphi and Kylix versions, i.e. it have parsed 
all the units in the <DELPHI LOCATION>\LIB directory and in the packages of 
the directory with no errors reported. See alllib<N>.bat files for examples 
of running the DCU32INT to parse all .\LIB.

This success doesn't mean, however, that the underlying DCU specification is 
absolutely correct. So, please, send me your bug reports (see the section 
"Home page" for details).


History.
--------

In 1996 I first saw Delphi (1.0 and 2.0).

Before that time, in the beginning of 1994 I had a successful experience
of reconstructing the structure of TPU files. I was interested in
the methods used by Borland to effectively compile Pascal programs
(much more effective in the speed of compilation, than C compilers
do). The conclusion I have made then was that it is possible to 
reconstruct an unknown file structure when you have at hand a generator 
of these files (a compiler for the case of the Pascal units).

The structure of 16-bit DCU for Delphi 1.0 is similar (with minor
changes) to the structure of TPU files, so I have easily extended
the TPU viewer program to include the DCU files.

But the structure of 32-bit DCU was completely different, it gave
a reason to believe in the rumors that Borlands have bought somebody's
else technology to compile 32-bit code, and my first impression was
that it would be almost impossible to restore the structure of the
32-bit units. But I haven't had the FlexT system then.

Using FlexT it becomes very easy to experiment with the DCU32 format
specification. All I need to check the existence and correctness of some
data structure is to specify it in FlexT and to parse the test files.
No programming of reading and print procedures and recompiling of the
parse program is required.

So I started from the obvious from a cursory examination of the DCU32
files fact: the file begins with some header and then follows a list of
tagged records of different formats, the structure of which depend on the
tag. To analyze the structure of the record for some particular tag
we can always ask the compiler to generate as many such records as we need.

The fact, which made it very difficult to start the analysis of DCU32
files, is the extensive use of the data structures, which, by analogy with
OBJ files, I call indices. I.e. all the integer fields, which can take
more than 1 byte value, are encoded in the DCU32 file so, that 1s in the
first bits of the first byte (up to 4 bits) indicate, that additional bytes
are used to represent the value. So, the value of 0x12 is represented by one
byte with the value of 0x24, 0x1243 - two bytes: 0x0D 0x49, and so on,
and for the values which require all 4 bytes (more than 28 bits), the high
4 bits of the 1st byte are not used and filled with zeroes, and the value
is represented directly by the next 4 bytes. Subsequently, in Delphi 4.0
the 64-bit integers were introduced, to encode their values the 1st byte
takes the value of 0xFF, and the next 8 bytes represent the value.

So, the size of any data structure may differ depending on the values of
the indices contained in it, and to successfully parse the DCU32 file it is
required to detect these index fields. To do it I have written and analyzed
some test files which contain something like the following code:

const
  c1=$1;
  c12=$12;
  c123=$123;
  c1234=$1234;
  c12345=$12345;
  c123456=$123456;
  c1234567=$1234567;
  c12345678=$12345678;
  cm1=-$1;
  cm12=-$12;
  cm123=-$123;
  cm1234=-$1234;
  cm12345=-$12345;
  cm123456=-$123456;
  cm1234567=-$1234567;
  cm12345678=-$12345678;

The index field can be detected by the changes in its size and by
the fact that it often takes successive even numbers in the successive
records of the same type.

Detecting the record structure doesn't mean detecting its semantics.
The values of some indices remain uninterpreted. The criterion for
correctness is the successful parsing of the test file, without breaks
in the sequence of tagged records caused by the wrong size detected
for some record.

The results of this analysis are represented in the DCU32.rfi FlexT file.
Unfortunately, not all the information can now be represented in FlexT.
In particular, it can't represent the fact that some tagged records are
enumerated by the two number sequences: the sequence of data types and
the sequence of addresses, with the data type declaration being a member
of both sequences (the address for data type is reserved for its TypeInfo).
A lot of indices in the DCU32 records are references to addresses and data
types using these sequence numbers.

So, to completely restore the structure of DCU32 files, it was anyway
required to write the specialized program DCU32INT, which extracts almost
all the information in readable and close to Pascal syntax form. I call
this program DCU32INT and not DCU32PAS, because it is always possible
to extract an interface part of a DCU32 file, but I doesn't claim yet
that it can extract a Pascal file, which can be immediately compiled
to obtain the same DCU (a lot of work should be done to make it possible).

All the rest of the reconstruction of the DCU32 format had been done using
the DCU32INT program, but the FlexT parse results were still used when
something went wrong. The most complex thing here was to understand the
rules of assignment of the numbers in the sequence of addresses.


The Compiler Information Loss Limitations.
------------------------------------------

There are two kinds of limitations of the DCU32INT program:

1. The ones caused by some disadvantages in its implementation,
  which can be overcome later;
2. The ones caused by information loss in DCU after compiling Pascal
  source, which are inevitable.

Here we'll consider some of the latter limitations.

While converting Pascal source into DCU, Delphi compiler extracts from
source and stores into DCU only the information, which is necessary to
produce later an executable file and also, if required, a debug
information for this file. During this process the compiler performs
some simplifications, which cause information loss.

Examples:

1. Identifiers declared in implementation part and in subroutines are
  discarded if the debug info checkbox doesn't checked.

2. Evaluation of expressions. Constant expressions are replaced by their
  values, so one can't determine, e.g. that CDM_FIRST = WM_USER+100,
  it will have the fixed value of $0464.
  
3. Resolution of rename types. The rename types (types, which are defined
  by declarations like THandle = integer), are replaced by their reference
  type, so all the references to the THandle type in the source code are
  replaced by the System.Integer type.
  
4. Merge of fields in the records with variants. The declaration like

  TVarRec = record
    case Byte of
      vtInteger:    (VInteger: Integer; VType: Byte);
      vtBoolean:    (VBoolean: Boolean);
    -----------------------------------------
  end;

  Is stored as

  TVarRec{88,7F9FF4C2}=record
    VInteger: Integer{F:2 Ofs:0};
    VType: Byte{F:2 Ofs:4};
    VBoolean: Boolean{F:2 Ofs:0};
    ----------------------------------
  end;

  where Ofs:_ is a field offset information. Of course, we can group
  the fields into cases according to their order and offsets (this
  capability is now supported by DCU32INT), and TVarRec is parsed as

  TVarRec=record 
    case Integer of
     0: (
       VInteger: Integer;
       VType: Byte);
     1: (
       VBoolean: Boolean);
    ----------------------------------
  end;

  But the information about the case labels is lost here completely, 
  and it can't be used, e.g. to display safely (Delphi version 
  independent) the Variant type value using the TVarData definition.

All the above mentioned limitations can be demonstrated by Delphi
browser and evaluator (those utilities are also limited by them).

So, the extracted Pascal code can cause some problems, if used in other
version of Delphi, than that, which produced the DCU.

Code templates and inline encoding.
-----------------------------------

Delphi 2009 and higher Delphi versions support inline procedures and code 
templates. The information required to call inline procedure by inserting 
its code into the place where it is called is encoded in the drConstAddInfo
records. All I know by now about this encoding is how to skip its data safely.
We can easily ignore the inline encoding because the compiler may decide
to call inline procedure as a regular procedure, so the inline procedures
are also provided with the regular code, it is this code which is used by 
DCU32INT to show the procedure algorithm.

But the algorithms of template code are stored in DCU using the inline 
encoding or something like this only, without regular code. The files 
Generics.Collections.dcu and Generics.Defaults.dcu contain the examples 
of this kind. So, the template algorithms can't be shown by the current 
version of DCU32INT. It would be interesting to explore the inline 
encoding by drConstAddInfo in more details. I would greatly appreciate if
somebody will do this analysis.

UPDATE: I have implemented the decompiler of inline code since DCU32INT 1.24.1.

Besides from the templates the DCU will contain all the instantiations of
the templates after data type substitutions, which are used in the file.
The types of templates with parameters substituted are denoted by the names 
with "`" and digits, which replace the template parameters in the "<>" braces.
It would be better to hide the data types of this kind, to be more close 
to the original source, but the current version of DCU32INT shows them all, 
because I think that all this stuff is very interesting and helps to 
understand how the templates work.


Collaboration.
--------------

If you'll create something useful using my program or information contained
in its source, or will substantially improve this program, please, send me 
your results. I'll publish all such programs, which I'll consider to be 
useful, at my site (including the links to their home pages, if available,
and/or other author information).

I can propose the following lines of improvements, which I'm not going to
develop myself in the nearest future:

1. The DCU32INT is a console application, but it would be interesting to 
  create some kind of DCU Browser as a GUI application.

2. The disassembler, which is used in DCU32INT, is rather simple, it would be 
  useful to improve it using some data flow analysis techniques.

3. The ideal final result for this kind of program would be to restore 
  completely the Pascal source from DCU. Of course this problem 
  is VERY hard. More simple problem, but still VERY hard, is to produce
  the Pascal source, where all the procedures are ASSEMBLER. The easier 
  approach is to produce assembler procedures which could have incorrect
  semantics (e.g. DB instead of some opcodes), but still could be compiled 
  to produce correct executable. Note, that the result of this kind of 
  program would be Delphi version dependent.

4. Compiler discards some names, and other information. We could create
  additional input file for DCU32INT, which could contain some additional 
  guess information for the DCU being parsed, e.g. names of unnamed local 
  variables or types, entry points in the procedure code or even additional 
  type declarations (which could be compiled using DCC32 into additional DCU
  and then extracted from it).

5. Personally, I prefer to use Delphi and not C++ Builder, but it could be 
  interesting to modify this program to enable it generate its output in 
  the close to C++ Builder syntax form (C++ syntax + Borland's extensions) 
  instead of Pascal. This program could be used to partially transfer
  programs frop Pascal to C through DCU.

6. The encoding of inline procedures is ignored now. To be able to show
  template code it is required to understand it in more details (see 
  "Code templates and inline encoding").


Home page.
----------

The latest version of this program and all the related news will be available
at http://hmelnov.icc.ru/DCU/

Please, send me (e-mail: alex@icc.ru) bug reports (including the units 
which were not parsed correctly), but first check:
  1. that you have the latest version of DCU32INT,
  2. that this bug was not already reported at 
     http://hmelnov.icc.ru/DCU/FAQ.htm.

------------------------------------------------------------------------

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
