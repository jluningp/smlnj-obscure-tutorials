# Obscure SML/NJ Tutorials 

So many SML/NJ features are very poorly documented or their documentation is out of date. For my own sanity, I'm writing
some tutorials (and maybe also documentation) for features that I find useful but that are very difficult to 
find documentation for. 

## Tutorials 

* Pretty Printing: The SML/NJ REPL allows you to install custom pretty printers for monomorphic datatypes. This is very 
                   nice if you ever have an abstract datatype in a structure, since the default pretty print is `-`.
* Parsing: The SML/NJ Visible Compiler gives you access to a lot of compiler internals, including parsing. Here's a 
           simple way to run the parser. 
