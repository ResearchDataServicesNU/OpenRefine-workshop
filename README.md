# Installing OpenRefine

Go to [https://openrefine.org/download](https://openrefine.org/download) to download the appropriate version of OpenRefine for your computer.

OpenRefine is a free and open-source software that runs exclusively on your own computer, and does not upload any of your data to any other server. It uses a web browser for its interface, but it does not require an internet connection for most of its features. (It _does_ use the internet if you ask it to "reconcile" your data with an online source.)

Follow your computer's prompt to install the software.

# Setting up our practice project

## Getting a dataset

OpenRefine can help with a wide range of data formats, including JSON and XML, but for this workshop we'll be focusing on "spreadsheety" data. If you have an Excel file, a CSV, or a TSV, you can use it to follow along with this workshop.

To practice with the same dataset I'll be using in the workshop, download `arsenical-books.csv` from this GitHub repository. This data is taken from the University of Delaware's [Poison Book Project](https://sites.udel.edu/poisonbookproject/arsenical-books-database/). Save this file anywhere on your computer that will let you find it later.

## Starting a "project"

By default, OpenRefine starts up in "Create project" with "Get data from This Computer" selected, so we can just click "Browse" to find the spreadsheet file.

Clicking "next" brings us to a preview page where we can confirm that OpenRefine has interpreted our file correctly.

"Create project" confirms those settings and brings us to the main OpenRefine interface.

# Exploring our data

Let's start by getting comfortable with "facets" and "filters," and the OpenRefine interface for viewing subsets of our data.

* Are our OCLC numbers usable as unique identifiers?
   * OCLC No. > Facet > Text facet
   * Sort facet by "count"
* What year(s) had the most arsenical books?
   * Date > Edit cells > Common transformations > To number
   * Date > Facet > Text facet; sort by "count"
   * Date > Facet > Numeric facet
* What arsenical books does the Boston Athaneum own?
   * Owner > Text facet
   * Owner > Text filter
   * Type "Boston" in the text filter
   * (Click "25" next to "Show:" at the top to see all of them)

# Modifying ("cleaning") our data

* Editing a single cell
   * Looking at the books owned by the Boston Athaneum, there's an inconsistency: a comma is used to separate two owners when it should be a semicolon
   * Hover your mouse over the relevant cell of data and an "edit" button will pop up.
* Editing all identical cells
   * When we looked at dates, the numerical facet ignored the ones that had brackets, question marks, and other annotations. That interferes with a numerical analysis.
   * Clicking "[1854]" in the "Date" facet will change out view to just see the three related rows of data.
   * Click "edit" on any of the cells to change it from "[1854]" to "1854", and this time click "Apply to all identical cells"
* Editing all identical cells, directly from the facet
   * Look for "[1867]" in the "Date" facet. This time instead of clicking the number in order to filter our data view, hover over it and see the "edit" button that appears.
   * Click "edit" in the facet to delete the brackets, and click "apply".
* Editing all cells in a subsetted view
   * Let's say we want to remove all the brackets, _except_ where there is also a question mark indicating that the supplied date is uncertain.
   * Date > Text filter
   * Type "?" in the text filter
   * Click "invert" so it filters to just the rows that _don't_ have "?"
   * (Optional for smooth viewing: Date > Text filter again; this time type "[" in the new filter)
   * Date > Edit cells > Transform...
   * Type `value.replace("[","")`
   * Repeat with `value.replace("]","")`
* Let's finish making things numerical...
   * Date > Edit cells > Common transformations > To number
   * In the Date numeric facet, uncheck everything but "non-numeric" to see which dates still can't be converted to numbers.
   * There are four years with "c." in front and three with "Nd" in front. Use any of the methods we've seen so far to remove those parts.
   
# Undoing and redoing our data modification

Let's say we really regret losing our informations about which dates had brackets around them, but we still want to have access to the "cleaned" years.

* Save the steps we've done
   * Undo / Redo > Extract 
   * Make sure the boxes are checked for all the steps we'll want to repeat later
   * Either click "export" to download a JSON file, or copy-paste all the text somewhere for safekeeping
   * Close
* Backtrack to a previous state of the project
   * In the Undo / Redo tab, click the oldest step that you want to _keep_
* Duplicate the Date column so we have the original for safekeeping
   * Date > Edit column > Add column based on this column
   * New column name: "Original date"
   * Note that this "overwrites" our previous changes so we can so longer simple redo them from the Undo/Redo panel!
* Re-clean the Date column the way we did before
   * Undo / Redo > Apply
   * Either upload the JSON you downloaded earlier, or paste in the text you copied
   * Perform Operations
* Explore facets and filters to check that it worked as we expected!
   * In particular, make sure that our "Original date" column has the _whole_ original column in it, and not a faceted/filtered subset. (You can use "Reset all" in the Facet/Filter pane to make sure you have the entire dataset 'active'.)
   
# More common use cases

* Splitting imprint info into "City" and "Publisher" columns
   * Edit column > Split into several columns
      * by separator, ":"
      * Split into 2 columns at most
   * Imprint 1 > Edit column > Rename this column... "Imprint city"
   * Imprint 2 > Edit column > Rename this column... "Imprint publisher"
   * Imprint publisher > Edit cells > Common transformations > Trim trailing and leading whitespace
   * Imprint city > Edit cells > Common transformations > Trim trailing and leading whitespace
* Changing capitalization
   * Arsenical material > Edit cells > Common transformations > to lowercase
   * Title > Edit cells > Common transformations > to titlecase
      * (Note that this does what a computer scientists thinks title case is: capitalizing the first letter of _every_ word)
* Making author names consistent
   * Author > Edit cells > Transform...
   * Type `value.split(/,\s+/).reverse().join(" ")` (or really, copy-paste it)
   * Author > Edit cells > Common transformations > Collapse consecutive whitespace
      
# Clustering!!

* Author > Edit cells > Cluster and edit...
   * Method: Key collision; Keying function: Fingerprint
      * actually, let's Close
      * Author > Edit cells > Common transformations > Collapse consecutive whitespace
   * Method: Key collision; Keying function: Cologne phonetic
      * Check the "Merge?" box next to the two variants of Mrs. S.C. Hall
      * Click "Merge selected & re-cluster"
   * Method: Nearest neighbor; Distance function: Levenshtein
      * The matches get "looser" when Radius gets bigger and Block chars gets smaller
      * Mouse over a set of names and click "browse cluster" for more context on whether a merge is justified
      
# Data extension and augmentation (with WikiData)

At this point, rather than asking you to follow along, I'll demonstrate some more complex features as inspiration for what OpenRefine can do.

Some resources to learn more:

* [OpenRefine's documentation about reconciliation](https://openrefine.org/docs/manual/reconciling)
* [A tutorial on data augmentation with WikiData](https://mdl.library.utoronto.ca/technology/tutorials/openrefine-augmenting-activity-2-using-reconciliation-services)
* [Resources on reconciling data on scholarly journals](https://librarycarpentry.org/lc-open-refine/13-looking-up-data.html)
* [Community document with useful OpenRefine GREL functions](https://docs.google.com/document/d/1sLklZKeWxD6AbyLQz7d4o7JLyfxEMkTjgPd5OVS9jSk/edit#heading=h.ielr0i6hygmb)