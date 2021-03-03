# Write Composable Command Line Scripts

This is subject to proofreading! If you're comfortable with the command line
and doing some coding, this checklist will ensure you're keeping your standards
high.

## The Checklist

- [ ] It knows how to `--help`.
- [ ] It prefers to be silent.
- If it works on text:
    - [ ] output goes to *stdout*
    - [ ] input comes from *stdin*
    - [ ] debugging / error messages go to *stderr*
- If it works on file-systems, databases, etc: 
    - [ ] debugging / error messages go to *stderr*
- [ ] It's a library


## Elaboration

The command line has one major advantage over GUIs and other interfaces.
Composable scripts.  You can chain together small programs in different
combitations to accomplish goals, similar to the way you can combine different
combinations of words to say different things. If you want to take that
advantage, while building tools that other people can use, these guidelines
should keep you on track.

### Know how to `--help`

Does this require explanation?  If someone runs your program with `-h` or
`--help`, they are asking for a reminder of how to use it.  Give it them.
Ideally you've got one sentence explaining whet the script does, a usage
synopsis showing your option and argument structure, and a short list of
options that can be accessed with a single or double dash.  That's a single
dash for the one character flag, and two for the full words.  For example,
here's the help for `cat`:

    $ cat --help
    Usage: cat [OPTION]... [FILE]...
    Concatenate FILE(s) to standard output.
    
    With no FILE, or when FILE is -, read standard input.
    
      -A, --show-all           equivalent to -vET
      -b, --number-nonblank    number nonempty output lines, overrides -n
      -e                       equivalent to -vE
      -E, --show-ends          display $ at end of each line
      -n, --number             number all output lines
      -s, --squeeze-blank      suppress repeated empty output lines
      -t                       equivalent to -vT
      -T, --show-tabs          display TAB characters as ^I
      -u                       (ignored)
      -v, --show-nonprinting   use ^ and M- notation, except for LFD and TAB
          --help     display this help and exit
          --version  output version information and exit
    
    Examples:
      cat f - g  Output f's contents, then standard input, then g's contents.
      cat        Copy standard input to standard output.
    
    GNU coreutils online help: <https://www.gnu.org/software/coreutils/>
    Full documentation at: <https://www.gnu.org/software/coreutils/cat>
    or available locally via: info '(coreutils) cat invocation'

### Prefer to be silent

It's generally rude to spam people with "information" they didn't explicitly
request.  That goes double if they might be trying to use the output of one
script as the input for another.

If something has gone so badly that the script can't run, go ahead and send an
`error` to stderr.  If there is a strong indication that the output is somehow
wrong, and it can't be corrected, go ahead and send a `warning` to stderr.
Otherwise, prefer to give only the explicitly requested output.

Some users get worried when they see long-running scripts with no output.  If
your script is stable, they'll grow out of it; in the meantime, if you are
really worried about them, add a `--verbose` option.  Just make sure that any
`debug` or `info` messages go to stderr and not stdout!

### Text in, text out

This is where the show really happens.  If your script works on text, it can be
combined with other scripts on one condition:  text input via stdin, and output
via stdout. Tools like `wc` (counts words) and `grep` (searches for matching
strings) do this: so if I want to know how many files are in my home directory
`ls | wc -l`, or how many lines of code I changed on a file in my git repo,
`git blame importer.py | grep "Pablo Virgo" | wc -l`.

You get the idea.

If you're working with text, prefer to take input via stdin and output to
stdout.  You'd rather not have errors, but when they happen, stderr.

#### But not everything is text!

Got that right.  If you're listing files off your hard drive or shunting
information into a database, you're going to want to pick a different
input or output location.  Do the right thing for the job at hand - but it's
worth bearing in mind that a lot of interesting composition possibilities open
up if your script is able to keep even one side of the I/O process in the world
of text streaming: check out [singer.io](https://singer.io) (no affiliation) to
get a sense of the possibilities.


### Bonus: make it a library

For bonus points, structure your script so that the primary functionality can
be imported as a library in its native language.  This will pave the road for
reuse with any future interfaces and help exercise your good architecture
habits.  You know you've got it right if:

1. The CLI for your script is a thin wrapper that parses a few arguments and
calls the right imported functions.
2. The library itself has no dependency on the CLI.
3. Someone who installs your tool with the conventions of its native language
can immediately either run it as a script or import it as part of another
application.


## Get out there and do something great

I hope this is helpful.  When you reach the point where you're comfortable
writing large scale applications and libraries, remember that excellence in the
small things still counts.


Take care.
