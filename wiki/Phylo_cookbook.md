---
title: Phylo cookbook
permalink: wiki/Phylo_cookbook
layout: wiki
tags:
 - Cookbook
---

Here are some examples of using [Bio.Phylo](Phylo "wikilink") for some
likely tasks. Some of these functions might be added to Biopython in a
later release, but you can use them in your own code with Biopython
1.54.

Convenience functions
---------------------

### Index clades by name

For large trees it can be useful to be able to select a clade by name,
or some other unique identifier, rather than searching the whole tree
for it during each operation.

``` python
def lookup_by_names(tree):
    names = {}
    for clade in tree.find_clades():
        if clade.name:
            if clade.name in names:
                raise ValueError("Duplicate key: %s" % clade.name)
            names[clade.name] = clade
    return names
```

Now you can retrieve a clade by name in constant time:

``` python
tree = Phylo.read('ncbi_taxonomy.xml', 'phyloxml')
names = lookup_by_names(tree)
for phylum in ('Apicomplexa', 'Euglenozoa', 'Fungi'):
    print "Phylum size", len(names[phylum].get_terminals())
```

A potential issue: The above implementation of lookup\_by\_names doesn't
include unnamed clades, generally internal nodes. We can fix this by
adding a unique identifier for each clade. Here, all clade names are
prefixed with a unique number (which can be useful for searching, too):

``` python
def tabulate_names(tree):
    names = {}
    for idx, clade in enumerate(tree.find_clades()):
        if clade.name:
            clade.name = '%d_%s' % (idx, clade.name)
        else:
            clade.name = str(idx)
        names[clade.name] = clade
    return clade
```

Comparing trees
---------------

*TODO:*

-   Symmetric difference / partition metric, a.k.a. topological distance
-   Quartets distance
-   Nearest-neighbor interchange
-   Path-length-difference

Consensus methods
-----------------

*TODO:*

-   Majority-rules consensus
-   Adams ([Adams
    1972](http://www.faculty.biol.ttu.edu/Strauss/Phylogenetics/Readings/Adams1972.pdf))
-   Asymmetric median tree ([Phillips and Warnow
    1996](http://www.springerlink.com/content/y1x70058822qg257/))

Rooting methods
---------------

*TODO:*

-   Root at the midpoint between the two most distant nodes (or "center"
    of all tips)
-   Root with the given outgroup (terminal or nonterminal)

Graphics
--------

*TODO:*

-   Party tricks with `draw_graphviz`, covering each keyword argument

Exporting to other types
------------------------

### Convert to a PyCogent tree

The tree objects used by Biopython and PyCogent are different.
Nonetheless, both toolkits support the Newick file format, so
interoperability is straightforward at that level:

``` python
from Bio import Phylo
import cogent

Phylo.write(bptree, 'mytree.nwk', 'newick')  # Biopython tree
ctree = cogent.LoadTree('mytree.nwk')        # PyCogent tree
```

*TODO:*

-   Convert objects directly, preserving some PhyloXML annotations if
    possible

### Convert to a NumPy array or matrix

*TODO:*

-   Adjacency matrix: cells are True if parent-child relationship
    exists, otherwise False
-   Distance matrix: cells are branch lengths if a branch exists,
    otherwise Inf or NaN
-   Relationship matrix? See [Martins and Housworth
    2002](http://www.jstor.org/stable/3070822)
