# Pretty Printers 

This tutorial on installing pretty printers for SML/NJ works for version 110.82. A similar but
modified procedure works for versions prior to 110.80. 

## Structures 

The following structures are used to install pretty printers in the SML/NJ REPL. 

```
structure CompilerPPTable
install_pp : string list -> (PrettyPrint.PP.stream -> 'a -> unit) -> unit

structure PrettyPrint
string : stream -> string -> unit
...
```

`CompilerPPTable.install_pp` takes in a path to a (monomorphic, generative) datatype, a pretty printing function which takes 
in a stream and a value of that type and prints it, and adds that pretty printer to the pretty printing table. 
Whenever a value of that type is typed into the REPL, this pretty printer will be looked up in the pretty printing table 
and called.

`PrettyPrint.string` is the primary method of creating a pretty printer. It should be called from within the 
pretty printing function with the string that should be output for that input value. 

## Example 

```
structure Nat = 
struct
   datatype nat = Zero | Successor of nat 

   fun toInt Zero = 0
     | toInt (Successor n) = (toInt n) + 1

   fun toString n = Int.toString (toInt n)

   fun pretty_print pps n = PrettyPrint.string pps (toString n)
end
```

Here we create a structure for natural numbers, represented as zero and successor. 

We'd like to pretty print these nats in the same way that we pretty print integers. To do this, we define a toString 
function for nats that first converts them to ints and then calls the integer toString function. We then define 
a pretty printer, Nat.pretty_print, which takes in a PrettyPrint.pp_stream and a nat, and uses PrettyPrint.string 
to output the string. We can then install this pretty printer in the SML/NJ REPL as follows:

```
Standard ML of New Jersey v110.82 [built: Mon Apr 23 16:23:09 2018]
- use "nat.sml";
[opening nat.sml]
structure Nat :
  sig
    datatype nat = Successor of nat | Zero
    val toInt : nat -> int
    val toString : nat -> string
    val pretty_print : ?.PrettyPrint.PP.stream -> nat -> unit
  end
val it = () : unit
- CompilerPPTable.install_pp ["Nat", "nat"] (Nat.pretty_print);
[autoloading]
[autoloading done]
val it = () : unit
- Nat.Successor (Nat.Successor (Nat.Zero));
val it = 2 : Nat.nat
```
We use the single function in the CompilerPPTable structure to add our pretty printer to the pretty printing table
for Nat.nat. The input string list should represent the longid of the datatype (the path to the datatype, including
all the structures it's contained in and its own name).

## Limitations

Pretty printers can only be installed for monomorphic datatypes, which greatly limits their usefulness. It'd be 
wonderful to be able to pretty print custom-defined lists, sequences, etc, but sadly this isn't possible.

