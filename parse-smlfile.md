# Parsing

This tutorial demonstrates how to parse SML code from inside the SML/NJ repl or an SML file. The Visible Compiler 
of SML/NJ gives access to a significant portion of the compilers internals, including parsing. It's sometimes useful to 
have access to the AST of your (or someone else's code), for Autograding, static analysis, or some shadier hacks. 

## Structures 

```
SmlFile 
parseOne : Source.inputSource -> unit -> Ast.dec option
parse : Source.inputSource -> Ast.dec

Source
newSource : string * TextIO.instream * bool * ?.PrettyPrintNew.PP.device -> inputSource

ErrorMsg 
defaultConsumer : unit -> ?.PrettyPrintNew.PP.device
...

TextIO
openIn : string -> instream 

Ast
datatype dec = ...
```

`SmlFile` is where the parsing functions actually live, but to get a valid input to these functions, we need to take a 
trek through several other structures to produce a Source.inputSource. 

`Source` is the structure we use to produce a new source. newSource takes in a couple of arguments - let's go through
them one at a time. 

```
Source.newSource filename stream interactive errorPrinter 
```

`filename` and `stream`: When we parse an SML file, we want more than just the plain AST back - we want a "Marked" AST that keeps track of locations in the source code so we can have helpful error 
reporting. So, we pass in both the file name and the input stream, which we can get by opening the file using 
TextIO.openIn (probably the most normal function we'll use here). 

`interactive` is a bit mysterious. The SML/NJ evaluation loop does some strange voodoo with TextPrimIO, casing on
something in the RD record to decide if this is interactive or not. To be perfectly honest, I have no idea what it's 
actually doing. But, for our purposes, since we're not actually implementing or modifying SML/NJ, we can safely set
interactive to false. 

`errorPrinter` is a pretty printer device that will recieve any error output. There may be cases where you want this 
to be something other than the default pretty printer device, but we won't cover those here. 

TextIO and ErrorMsg are used for input/output of the file we're loading and error messages respectively. 

`Ast` is where the SML AST is defined. This is a basic parse tree. However, I'll briefly mention SML/NJ's
Backend.Compiler.elaborate function, which can be used to produce an elaborated AST, an Absyn.dec.

## Example

We can create a function to parse a file by doing the following

```
fun parseFile filename = 
   let 
      val stream = TextIO.openIn filename
      val interactive = false
      val consumer = ErrorMsg.defaultConsumer ()
      val source = Source.newSource (filename, stream, interactive, consumer)
   in
      SmlFile.parse source
   end
```
## Notes

The Ast is in a somewhat unprocessed form: FlatAppExp and FlatAppPat (unprocessed infix) abound, and variables are 
not yet unique symbols. If you want to process the Ast further, there is a structure called `Backend.Compile` which 
contains an `elaborate` function. Depending on your purposes, though, it might be easier to simply elaborate the AST
yourself.

## Limitations 

As far as I can tell, there isn't a way to feed the Ast back into the REPL once you've parsed it. I'm still digging,
but it seems to not be possible at the moment. 
