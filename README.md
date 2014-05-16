# Codox

A tool for generating API documentation from Clojure or ClojureScript
source code.

## Examples

Some examples of API docs generated by Codox in real projects:

* [Compojure](http://weavejester.github.com/compojure/)
* [Hiccup](http://weavejester.github.com/hiccup/)
* [Ring](http://ring-clojure.github.com/ring/)

## Usage

Include the following plugin in your `project.clj` file or your global
profile:

```clojure
:plugins [[codox "0.8.3"]]
```

Then run:

```
lein doc
```

This will generate API documentation in the "doc" subdirectory.


## AOT Compilation

AOT-compiled namespaces will lose their metadata, which mean you'll
lose documentation for namespaces. Avoid having global `:aot`
directives in your project; instead, place them in a specialized
profile, such as `:uberjar`.


## Project Options

Codox can generate documentation from Clojure or ClojureScript. By
default it looks for Clojure source files, but you can change this to
ClojureScript by setting the `:language` key:

```clojure
:codox {:language :clojurescript}
```

By default Codox looks for source files in the `src` subdirectory, but
you can change this by placing the following in your `project.clj`
file:

```clojure
:codox {:sources ["path/to/source"]}
```

To exclude a namespace, use the `:exclude` key:

```clojure
:codox {:exclude my.private.ns}
```

Sequences work too:

```clojure
:codox {:exclude [my.private.ns another.private.ns]
```

To include only one or more namespaces, set them with the `:include` key:

```clojure
;; Again, a single symbol or a collection are both valid
:codox {:include library.core}
:codox {:include [library.core library.io]}
```

To write output to a directory other than the default `doc` directory, use the
`:output-dir` key:

```clojure
:codox {:output-dir "doc/codox"}
```

To use a different output writer, specify the fully qualified symbol of the
writer function in the `:writer` key:

```clojure
:codox {:writer codox.writer.html/write-docs}
```

If you have the source available at a URI and would like to have links
to the function's source file in the documentation, you can set the
`:src-dir-uri` key:

```clojure
:codox {:src-dir-uri "http://github.com/clojure/clojure/blob/master/"}
```

(Note that the ending "/" is required from version 0.6.5 onward.)

Some code hosting sites, such as Github, set an anchor for each line
of code. If you set the `:src-linenum-anchor-prefix project` key, the
function's "Source" link will point directly to the line of code where
the function is declared. This value should be whatever is prepended
to the raw line number in the anchors for each line; on Github this is
"L":

```clojure
:codox {:src-dir-uri "http://github.com/clojure/clojure/blob/master/"
        :src-linenum-anchor-prefix "L"}
```

For more control, you can assign mapping functions to source paths
that match a regular expression. This is particularly useful for
created source links from generated source code, such as is the case
with [cljx](https://github.com/lynaghk/cljx).

For example, perhaps your Clojure source files are generated in
`target/classes`. To link back to the original .cljx file, you could
add a mapping like:

```clojure
:codox {:src-uri-mapping {#"target/classes" #(str "src/" % "x")}}
```

(Note that the ending "/" is required in "src/".)


## Metadata Options

To force Codox to skip a public var, add `:no-doc true`
to the var's metadata. For example:

```clojure
;; Documented
(defn square
  "Squares the supplied number."
  [x]
  (* x x)

;; Not documented
(defn ^:no-doc hidden-square
  "Squares the supplied number."
  [x]
  (* x x))
```

You can also skip namespaces by adding `:no-doc true` to the
namespace's metadata. *This currently only works for Clojure code, not
ClojureScript.* For example:

```clojure
(ns ^:no-doc hidden-ns)
```

To denote the library version the var was added in, use the `:added`
metadata key:

```clojure
(defn square
  "Squares the supplied number."
  {:added "1.0"}
  [x]
  (* x x))
```

Similar, deprecated vars can be denoted with the `:deprecated`
metadata key:

```clojure
(defn square
  "Squares the supplied number."
  {:deprecated "2.0"}
  [x]
  (* x x))
```


## Docstring Formats

By default, docstrings are rendered by Codox as fixed-width plain
text, as they would be on a terminal. However, you can override this
behavior by specifying an explicit format for your docstrings.

Currently there are only two formats for docstrings: plaintext and
markdown. The markdown format includes extensions for code blocks,
tables, and, like the plaintext format, URLs are automatically encoded
as links.

You can specify docstring formats via a var's metadata, using the
`:doc/format` option:

```clojure
(defn foo
  "A **markdown** formatted docstring."
  {:doc/format :markdown}
  [x])
```

Or you can specify the docstring format globally by adding it to the
defaults map in your project.clj file:

```clojure
:codox {:defaults {:doc/format :markdown}}
```

Markdown docstrings also support wikilink-style relative links, for
referencing other vars. Vars in the current namespace will be matched
first, and then Codox will try and find a best match out of all the
vars its documenting.

```clojure
(defn bar
  "See [[foo]] and [[user/square]] for other examples."
  {:doc/format :markdown}
  [x])
```


## License

Copyright © 2014 James Reeves

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
