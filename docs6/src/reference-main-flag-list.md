<!---  PLEASE DO NOT EDIT DIRECTLY. EDIT THE .md.in FILE PLEASE. --->
<div>
<span class="quicklinks">
Quick links:
&nbsp;
<a class="quicklink" href="../reference-main-flag-list/index.html">Flag list</a>
&nbsp;
<a class="quicklink" href="../reference-verbs/index.html">Verb list</a>
&nbsp;
<a class="quicklink" href="../reference-dsl-builtin-functions/index.html">Function list</a>
&nbsp;
<a class="quicklink" href="../glossary/index.html">Glossary</a>
&nbsp;
<a class="quicklink" href="https://github.com/johnkerl/miller" target="_blank">Repository ↗</a>
</span>
</div>
# List of command-line flags

Here are flags you can use when invoking Miller.  For example, when you type

<pre class="pre-highlight-in-pair">
<b>mlr --icsv --ojson head -n 1 example.csv</b>
</pre>
<pre class="pre-non-highlight-in-pair">
{
  "color": "yellow",
  "shape": "triangle",
  "flag": "true",
  "k": 1,
  "index": 11,
  "quantity": 43.6498,
  "rate": 9.8870
}
</pre>

the `--icsv` and `--ojson` bits are _flags_.  See the [Miller command
structure](reference-main-overview.md) page for context.

Also, at the command line, you can use `mlr -g` for a list much like this one.

## Comments-in-data flags

Miller lets you put comments in your data, such as

    # This is a comment for a CSV file
    a,b,c
    1,2,3
    4,5,6

Notes:

* Comments are only honored at the start of a line.
* In the absence of any of the below four options, comments are data like
  any other text. (The comments-in-data feature is opt-in.)
* When `--pass-comments` is used, comment lines are written to standard output
  immediately upon being read; they are not part of the record stream.  Results
  may be counterintuitive. A suggestion is to place comments at the start of
  data files.


**Flags:**

* `--pass-comments
`: Immediately print commented lines (prefixed by `#`) within the input.
* `--pass-comments-with {string}
`: Immediately print commented lines within input, with specified prefix.
* `--skip-comments
`: Ignore commented lines (prefixed by `#`) within the input.
* `--skip-comments-with {string}
`: Ignore commented lines within input, with specified prefix.

## Compressed-data flags

Miller offers a few different ways to handle reading data files
	which have been compressed.

* Decompression done within the Miller process itself: `--bz2in` `--gzin` `--zin`
* Decompression done outside the Miller process: `--prepipe` `--prepipex`

Using `--prepipe` and `--prepipex` you can specify an action to be
taken on each input file.  The prepipe command must be able to read from
standard input; it will be invoked with `{command} < {filename}`.  The
prepipex command must take a filename as argument; it will be invoked with
`{command} {filename}`.

Examples:

    mlr --prepipe gunzip
    mlr --prepipe zcat -cf
    mlr --prepipe xz -cd
    mlr --prepipe cat

Note that this feature is quite general and is not limited to decompression
utilities. You can use it to apply per-file filters of your choice.  For output
compression (or other) utilities, simply pipe the output:
`mlr ... | {your compression command} > outputfilenamegoeshere`

Lastly, note that if `--prepipe` or `--prepipex` is specified, it replaces any
decisions that might have been made based on the file suffix. Likewise,
`--gzin`/`--bz2in`/`--zin` are ignored if `--prepipe` is also specified.


**Flags:**

* `--bz2in
`: Uncompress bzip2 within the Miller process. Done by default if file ends in `.bz2`.
* `--gzin
`: Uncompress gzip within the Miller process. Done by default if file ends in `.gz`.
* `--prepipe {decompression command}
`: You can, of course, already do without this for single input files, e.g. `gunzip < myfile.csv.gz | mlr ...`.  Allowed at the command line, but not in `.mlrrc` to avoid unexpected code execution.
* `--prepipe-bz2
`: Same as  `--prepipe bz2`, except this is allowed in `.mlrrc`.
* `--prepipe-gunzip
`: Same as  `--prepipe gunzip`, except this is allowed in `.mlrrc`.
* `--prepipe-zcat
`: Same as  `--prepipe zcat`, except this is allowed in `.mlrrc`.
* `--prepipex {decompression command}
`: Like `--prepipe` with one exception: doesn't insert `<` between command and filename at runtime. Useful for some commands like `unzip -qc` which don't read standard input.  Allowed at the command line, but not in `.mlrrc` to avoid unexpected code execution.
* `--zin
`: Uncompress zlib within the Miller process. Done by default if file ends in `.z`.

## CSV-only flags

These are flags which are applicable to CSV format.


**Flags:**

* `--allow-ragged-csv-input or --ragged
`: If a data line has fewer fields than the header line, fill remaining keys with empty string. If a data line has more fields than the header line, use integer field labels as in the implicit-header case.
* `--headerless-csv-output or --ho
`: Print only CSV data lines; do not print CSV header lines.
* `--implicit-csv-header or --headerless-csv-input or --hi
`: Use 1,2,3,... as field labels, rather than from line 1 of input files. Tip: combine with `label` to recreate missing headers.
* `--no-implicit-csv-header
`: Opposite of `--implicit-csv-header`. This is the default anyway -- the main use is for the flags to `mlr join` if you have main file(s) which are headerless but you want to join in on a file which does have a CSV header. Then you could use `mlr --csv --implicit-csv-header join --no-implicit-csv-header -l your-join-in-with-header.csv ... your-headerless.csv`.
* `-N
`: Keystroke-saver for `--implicit-csv-header --headerless-csv-output`.

## File-format flags

See the File formats doc page, and or `mlr help file-formats`, for more
about file formats Miller supports.

Examples: `--csv` for CSV-formatted input and output; `--icsv --opprint` for
CSV-formatted input and pretty-printed output.

Please use `--iformat1 --oformat2` rather than `--format1 --oformat2`.
The latter sets up input and output flags for `format1`, not all of which
are overridden in all cases by setting output format to `format2`.


**Flags:**

* `--asv or --asvlite
`: Use ASV format for input and output data.
* `--csv or -c
`: Use CSV format for input and output data.
* `--csvlite
`: Use CSV-lite format for input and output data.
* `--dkvp
`: Use DKVP format for input and output data.
* `--gen-field-name
`: Specify field name for --igen. Defaults to "i".
* `--gen-start
`: Specify start value for --igen. Defaults to 1.
* `--gen-step
`: Specify step value for --igen. Defaults to 1.
* `--gen-stop
`: Specify stop value for --igen. Defaults to 100.
* `--iasv or --iasvlite
`: Use ASV format for input data.
* `--icsv
`: Use CSV format for input data.
* `--icsvlite
`: Use CSV-lite format for input data.
* `--idkvp
`: Use DKVP format for input data.
* `--igen
`: Ignore input files and instead generate sequential numeric input using --gen-field-name, --gen-start, --gen-step, and --gen-stop values. See also the seqgen verb, which is more useful/intuitive.
* `--ijson
`: Use JSON format for input data.
* `--inidx
`: Use NIDX format for input data.
* `--io {format name}
`: Use format name for input and output data. For example: `--io csv` is the same as `--csv`.
* `--ipprint
`: Use PPRINT format for input data.
* `--itsv
`: Use TSV format for input data.
* `--itsvlite
`: Use TSV-lite format for input data.
* `--iusv or --iusvlite
`: Use USV format for input data.
* `--ixtab
`: Use XTAB format for input data.
* `--json or -j
`: Use JSON format for input and output data.
* `--nidx
`: Use NIDX format for input and output data.
* `--oasv or --oasvlite
`: Use ASV format for output data.
* `--ocsv
`: Use CSV format for output data.
* `--ocsvlite
`: Use CSV-lite format for output data.
* `--odkvp
`: Use DKVP format for output data.
* `--ojson
`: Use JSON format for output data.
* `--omd
`: Use markdown-tabular format for output data.
* `--onidx
`: Use NIDX format for output data.
* `--opprint
`: Use PPRINT format for output data.
* `--otsv
`: Use TSV format for output data.
* `--otsvlite
`: Use TSV-lite format for output data.
* `--ousv or --ousvlite
`: Use USV format for output data.
* `--oxtab
`: Use XTAB format for output data.
* `--pprint
`: Use PPRINT format for input and output data.
* `--tsv
`: Use TSV format for input and output data.
* `--tsvlite or -t
`: Use TSV-lite format for input and output data.
* `--usv or --usvlite
`: Use USV format for input and output data.
* `--xtab
`: Use XTAB format for input and output data.
* `-i {format name}
`: Use format name for input data. For example: `-i csv` is the same as `--icsv`.
* `-o {format name}
`: Use format name for output data.  For example: `-o csv` is the same as `--ocsv`.

## Flatten-unflatten flags

These flags control how Miller converts record values which are maps or arrays, when input is JSON and ouput is non-JSON (flattening) or input is non-JSON and output is JSON (unflattening).

See the Flatten/unflatten doc page for more information.


**Flags:**

* `--flatsep or --jflatsep {string}
`: Separator for flattening multi-level JSON keys, e.g. `{"a":{"b":3}}` becomes `a:b => 3` for non-JSON formats. Defaults to `.`.
* `--no-auto-flatten
`: When output is non-JSON, suppress the default auto-flatten behavior. Default: if `$y = [7,8,9]` then this flattens to `y.1=7,y.2=8,y.3=9, and similarly for maps. With `--no-auto-flatten`, instead we get `$y=[1, 2, 3]`.
* `--no-auto-unflatten
`: When input non-JSON and output is JSON, suppress the default auto-unflatten behavior. Default: if the input has `y.1=7,y.2=8,y.3=9` then this unflattens to `$y=[7,8,9]`.  flattens to `y.1=7,y.2=8,y.3=9. With `--no-auto-flatten`, instead we get `${y.1}=7,${y.2}=8,${y.3}=9`.
* `--xvright
`: Right-justify values for XTAB format.

## Format-conversion keystroke-saver flags

The letters `c`, `t`, `j`, `d`, `n`, `x`, `p`, and `m` refer to formats CSV, TSV, DKVP, NIDX, JSON, XTAB,
PPRINT, and markdown, respectively. Note that markdown format is available for
output only.

| In  out   | **CSV** | **TSV** | **JSON** | **DKVP** | **NIDX** | **XTAB** | **PPRINT** | **Markdown** |
|------------|---------|---------|----------|----------|----------|----------|------------|--------------|
| **CSV**    |         | `--c2t` | `--c2j`  | `--c2d`  | `--c2n`  | `--c2x`  | `--c2p`    | `--c2m`      |
| **TSV**    | `--t2c` |         | `--t2j`  | `--t2d`  | `--t2n`  | `--t2x`  | `--t2p`    | `--t2m`      |
| **JSON**   | `--j2c` | `--j2t` |          | `--j2d`  | `--j2n`  | `--j2x`  | `--j2p`    | `--j2m`      |
| **DKVP**   | `--d2c` | `--d2t` | `--d2j`  |          | `--d2n`  | `--d2x`  | `--d2p`    | `--d2m`      |
| **NIDX**   | `--n2c` | `--n2t` | `--n2j`  | `--n2d`  |          | `--n2x`  | `--n2p`    | `--n2m`      |
| **XTAB**   | `--x2c` | `--x2t` | `--x2j`  | `--x2d`  | `--x2n`  |          | `--x2p`    | `--x2m`      |
| **PPRINT** | `--p2c` | `--p2t` | `--p2j`  | `--p2d`  | `--p2n`  | `--p2x`  |            | `--p2m`      |

Additionally:

* `-p` is a keystroke-saver for `--nidx --fs space --repifs`.
* `-T` is a keystroke-saver for `--nidx --fs tab`.

## JSON-only flags

These are flags which are applicable to JSON format.


**Flags:**

* `--jlistwrap or --jl
`: Wrap JSON output in outermost `[ ]`.
* `--jvstack
`: Put one key-value pair per line for JSON output (multi-line output).
* `--no-jvstack
`: Put objects/arrays all on one line for JSON output.

## Legacy flags

These are flags which don't do anything in the current Miller version.
They are accepted as no-op flags in order to keep old scripts from breaking.


**Flags:**

* `--jknquoteint
`: Type information from JSON input files is now preserved throughout the processing stream.
* `--jquoteall
`: Type information from JSON input files is now preserved throughout the processing stream.
* `--json-fatal-arrays-on-input
`: Miller now supports arrays as of version 6.
* `--json-map-arrays-on-input
`: Miller now supports arrays as of version 6.
* `--json-skip-arrays-on-input
`: Miller now supports arrays as of version 6.
* `--jsonx
`: The `--jvstack` flag is now default true in Miller 6.
* `--jvquoteall
`: Type information from JSON input files is now preserved throughout the processing stream.
* `--mmap
`: Miller no longer uses memory-mapping to access data files.
* `--no-fflush
`: The current implementation of Miller does not use buffered output, so there is no longer anything to suppress here.
* `--no-mmap
`: Miller no longer uses memory-mapping to access data files.
* `--ojsonx
`: The `--jvstack` flag is now default true in Miller 6.
* `--quote-all
`: Ignored as of version 6. Types are inferred/retained through the processing flow now.
* `--quote-minimal
`: Ignored as of version 6. Types are inferred/retained through the processing flow now.
* `--quote-none
`: Ignored as of version 6. Types are inferred/retained through the processing flow now.
* `--quote-numeric
`: Ignored as of version 6. Types are inferred/retained through the processing flow now.
* `--quote-original
`: Ignored as of version 6. Types are inferred/retained through the processing flow now.
* `--vflatsep
`: Ignored as of version 6. This functionality is subsumed into JSON formatting.

## Miscellaneous flags

These are flags which don't fit into any other category.

**Flags:**

* `--from {filename}
`: Use this to specify an input file before the verb(s), rather than after. May be used more than once. Example: `mlr --from a.dat --from b.dat cat` is the same as `mlr cat a.dat b.dat`.
* `--infer-int-as-float or -A
`: Cast all integers in data files to floats.
* `--infer-no-octal or -O
`: Treat numbers like 0123 in data files as string "0123", not octal for decimal 83 etc.
* `--infer-none or -S
`: Don't treat values like 123 or 456.7 in data files as int/float; leave them as strings.
* `--load {filename}
`: Load DSL script file for all put/filter operations on the command line.  If the name following `--load` is a directory, load all `*.mlr` files in that directory. This is just like `put -f` and `filter -f` except it's up-front on the command line, so you can do something like `alias mlr='mlr --load ~/myscripts'` if you like.
* `--mfrom {filenames}
`: Use this to specify one of more input files before the verb(s), rather than after. May be used more than once.  The list of filename must end with `--`. This is useful for example since `--from *.csv` doesn't do what you might hope but `--mfrom *.csv --` does.
* `--mload {filenames}
`: Like `--load` but works with more than one filename, e.g. `--mload *.mlr --`.
* `--nr-progress-mod {m}
`: With m a positive integer: print filename and record count to os.Stderr every m input records.
* `--ofmt {format}
`: E.g. `%.18f`, `%.0f`, `%9.6e`. Please use sprintf-style codes for floating-point nummbers. If not specified, default formatting is used.  See also the `fmtnum` function and the `format-values` verb.
* `--seed {n}
`: with `n` of the form `12345678` or `0xcafefeed`. For `put`/`filter` `urand`, `urandint`, and `urand32`.
* `--tz {timezone}
`: Specify timezone, overriding `$TZ` environment variable (if any).
* `-I
`: Process files in-place. For each file name on the command line, output is written to a temp file in the same directory, which is then renamed over the original. Each file is processed in isolation: if the output format is CSV, CSV headers will be present in each output file, statistics are only over each file's own records; and so on.
* `-n
`: Process no input files, nor standard input either. Useful for `mlr put` with `begin`/`end` statements only. (Same as `--from /dev/null`.) Also useful in `mlr -n put -v '...'` for analyzing abstract syntax trees (if that's your thing).

## Output-colorization flags

Miller uses colors to highlight outputs. You can specify color preferences.
Note: output colorization does not work on Windows.

Things having colors:

* Keys in CSV header lines, JSON keys, etc
* Values in CSV data lines, JSON scalar values, etc in regression-test output
* Some online-help strings

Rules for coloring:

* By default, colorize output only if writing to stdout and stdout is a TTY.
    * Example: color: `mlr --csv cat foo.csv`
    * Example: no color: `mlr --csv cat foo.csv > bar.csv`
    * Example: no color: `mlr --csv cat foo.csv | less`
* The default colors were chosen since they look OK with white or black
  terminal background, and are differentiable with common varieties of human
  color vision.

Mechanisms for coloring:

* Miller uses ANSI escape sequences only. This does not work on Windows
  except within Cygwin.
* Requires `TERM` environment variable to be set to non-empty string.
* Doesn't try to check to see whether the terminal is capable of 256-color
  ANSI vs 16-color ANSI. Note that if colors are in the range 0..15
  then 16-color ANSI escapes are used, so this is in the user's control.

How you can control colorization:

* Suppression/unsuppression:
    * Environment variable `export MLR_NO_COLOR=true` means don't color
      even if stdout+TTY.
    * Environment variable `export MLR_ALWAYS_COLOR=true` means do color
      even if not stdout+TTY.
      For example, you might want to use this when piping mlr output to `less -r`.
    * Command-line flags `--no-color` or `-M`, `--always-color` or `-C`.

* Color choices can be specified by using environment variables, or command-line
  flags, with values 0..255:
    * `export MLR_KEY_COLOR=208`, `MLR_VALUE_COLOR=33`, etc.:
        `MLR_KEY_COLOR` `MLR_VALUE_COLOR` `MLR_PASS_COLOR` `MLR_FAIL_COLOR`
        `MLR_REPL_PS1_COLOR` `MLR_REPL_PS2_COLOR` `MLR_HELP_COLOR`
    * Command-line flags `--key-color 208`, `--value-color 33`, etc.:
        `--key-color` `--value-color` `--pass-color` `--fail-color`
        `--repl-ps1-color` `--repl-ps2-color` `--help-color`
    * This is particularly useful if your terminal's background color clashes
      with current settings.

If environment-variable settings and command-line flags are both provided, the
latter take precedence.

Please do mlr `--list-color-codes` to see the available color codes (like 170),
and `mlr --list-color-names` to see available names (like `orchid`).


**Flags:**

* `--always-color or -C
`: Instructs Miller to colorize output even when it normally would not. Useful for piping output to `less -r`.
* `--fail-color
`: Specify the color (see `--list-color-codes` and `--list-color-names`) for failing cases in `mlr regtest`.
* `--help-color
`: Specify the color (see `--list-color-codes` and `--list-color-names`) for highlights in `mlr help` output.
* `--key-color
`: Specify the color (see `--list-color-codes` and `--list-color-names`) for record keys.
* `--list-color-codes
`: Show the available color codes in the range 0..255, such as 170 for example.
* `--list-color-names
`: Show the names for the available color codes, such as `orchid` for example.
* `--no-color or -M
`: Instructs Miller to not colorize any output.
* `--pass-color
`: Specify the color (see `--list-color-codes` and `--list-color-names`) for passing cases in `mlr regtest`.
* `--value-color
`: Specify the color (see `--list-color-codes` and `--list-color-names`) for record values.

## PPRINT-only flags

These are flags which are applicable to PPRINT output format.


**Flags:**

* `--barred
`: Prints a border around PPRINT output (not available for input).
* `--right
`: Right-justifies all fields for PPRINT output.

## Separator flags

See the Separators doc page for more about record separators, field
separators, and pair separators. Also see the File formats doc page, or
`mlr help file-formats`, for more about the file formats Miller supports.

In brief:

* For DKVP records like `x=1,y=2,z=3`, the fields are separated by a comma,
  the key-value pairs are separated by a comma, and each record is separated
  from the next by a newline.
* Each file format has its own default separators.
* Most formats, such as CSV, don't support pair-separators: keys are on the CSV
  header line and values are on each CSV data line; keys and values are not
  placed next to one another.
* Some separators are not programmable: for example JSON uses a colon as a
  pair separator but this is non-modifiable in the JSON spec.
* You can set separators differently between Miller's input and output --
  hence `--ifs` and `--ofs`, etc.

TODO: auto-detect is still TBD for Miller 6.

Notes about line endings:

* Default line endings (`--irs` and `--ors`) are "auto"
  which means autodetect from the input file format, as long as the input
  file(s) have lines ending in either LF (also known as linefeed, `\n`, `0x0a`, or Unix-style) or CRLF (also known as
  carriage-return/linefeed pairs, `\r\n`, `0x0d 0x0a`, or
  Windows-style).
* If both `irs` and `ors` are `auto` (which is
  the default) then LF input will lead to LF output and CRLF input will lead to
  CRLF output, regardless of the platform you're running on.
* The line-ending autodetector triggers on the first line ending detected in
  the input stream. E.g. if you specify a CRLF-terminated file on the command
  line followed by an LF-terminated file then autodetected line endings will be
  CRLF.
* If you use `--ors {something else}` with (default or explicitly
  specified) `--irs auto` then line endings are autodetected on input
  and set to what you specify on output.
* If you use `--irs {something else}` with (default or explicitly
  specified) `--ors auto` then the output line endings used are LF on
  Unix/Linux/BSD/MacOSX, and CRLF on Windows.

Notes about all other separators:

* IPS/OPS are only used for DKVP and XTAB formats, since only in these formats
  do key-value pairs appear juxtaposed.
* IRS/ORS are ignored for XTAB format. Nominally IFS and OFS are newlines;
  XTAB records are separated by two or more consecutive IFS/OFS -- i.e.
  a blank line. Everything above about `--irs/--ors/--rs auto` becomes `--ifs/--ofs/--fs`
  auto for XTAB format. (XTAB's default IFS/OFS are "auto".)
* OFS must be single-character for PPRINT format. This is because it is used
  with repetition for alignment; multi-character separators would make
  alignment impossible.
* OPS may be multi-character for XTAB format, in which case alignment is
  disabled.
* TSV is simply CSV using tab as field separator (`--fs tab`).
* FS/PS are ignored for markdown format; RS is used.
* All FS and PS options are ignored for JSON format, since they are not relevant
  to the JSON format.
* You can specify separators in any of the following ways, shown by example:
  - Type them out, quoting as necessary for shell escapes, e.g.
    `--fs '|' --ips :`
  - C-style escape sequences, e.g. `--rs '\r\n' --fs '\t'`.
  - To avoid backslashing, you can use any of the following names:

          ascii_esc  = "\x1b"
          ascii_etx  = "\x04"
          ascii_fs   = "\x1c"
          ascii_gs   = "\x1d"
          ascii_null = "\x01"
          ascii_rs   = "\x1e"
          ascii_soh  = "\x02"
          ascii_stx  = "\x03"
          ascii_us   = "\x1f"
          asv_fs     = "\x1f"
          asv_rs     = "\x1e"
          colon      = ":"
          comma      = ","
          cr         = "\r"
          crcr       = "\r\r"
          crlf       = "\r\n"
          crlfcrlf   = "\r\n\r\n"
          equals     = "="
          lf         = "\n"
          lflf       = "\n\n"
          newline    = "\n"
          pipe       = "|"
          semicolon  = ";"
          slash      = "/"
          space      = " "
          spaces     = "( )+"
          tab        = "\t"
          tabs       = "(\t)+"
          usv_fs     = "\xe2\x90\x9f"
          usv_rs     = "\xe2\x90\x9e"
          whitespace = "([ \t])+"

* Default separators by format:

        Format   FS     PS     RS
        csv      ","    N/A    "\n"
        csvlite  ","    N/A    "\n"
        dkvp     ","    "="    "\n"
        json     N/A    N/A    N/A
        markdown " "    N/A    "\n"
        nidx     " "    N/A    "\n"
        pprint   " "    N/A    "\n"
        xtab     "\n"   " "    "\n\n"


**Flags:**

* `--fs {string}
`: Specify FS for input and output.
* `--ifs {string}
`: Specify FS for input.
* `--ips {string}
`: Specify PS for input.
* `--irs {string}
`: Specify RS for input.
* `--ofs {string}
`: Specify FS for output.
* `--ops {string}
`: Specify PS for output.
* `--ors {string}
`: Specify RS for output.
* `--ps {string}
`: Specify PS for input and output.
* `--repifs
`: Let IFS be repeated: e.g. for splitting on multiple spaces.
* `--rs {string}
`: Specify RS for input and output.
