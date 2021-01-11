# Command Line Data Wrangling

Wrangling data using only (unix) shell commands.

The focus is on plain text and CSV (interpreted broadly in that the C(omma) can
be any other kind of delimiter)!

## Tools of the trade

* `cut` = filter columns
* `sed` = replace (and much more)
* `grep` = filter rows
* `sort` = sort!
* `uniq` = count duplicate (with sort = crude group by)
* `paste` = join 2 files (line by line)
* `wc` = count lines or "words"
* `split` = split a file into pieces (less useful)

The limitation of most shell utilities for CSV is that:

* they are fundamentally line oriented
* they are limited to a fairly naive approach to delimiters

If your CSVs are well behaved and do not:

* include line terminators within fields (which is allowed by CSV!)
* have delimiter occurring with values (e.g. having , appear in a field value -
  again this is very allowed as long as field value is quoted)

Then there is no problem. If your CSVs are not like this it is still possible
these tools will be useful but you will have to be more careful.

## Key Concepts

Pipes! One of the great (computing design) ideas of all time:

> 1. We should have some ways of coupling programs like garden hose--screw in
> another segment when it becomes when it becomes necessary to massage data in
> another way.  This is the way of IO also. [Doug McIlroy, 1964] ([source](http://doc.cat-v.org/unix/pipes/))

Basic point: the unix shell (and all of the above commands) have great support
for feeding the output of one command into the input of another and doing so in
a streaming "pipe-like" manner.

Example:
```bash
head -n20 file.txt | tail -n5 | cut -c1-10
```

This take first 20 lines of file.txt pipes that into tail which takes first 5
lines of that pipes that into cut which take characters 1-10 of each line. The
result: characters 1-10 of lines 15-20.


## Examples

### Delete lines at Start or End

If continuous lines at top and bottom of the file use head or tail.

Delete first line:
```bash
tail -n +2 {file}
```

Delete last line:
```bash
head -n +2 {file}
```

### Delete Lines Generally

Delete the nth line:
```bash
sed nd {file}
```

Delete a range of lines (n-m):
```bash
sed n,md {file}
```

Multiple deletes (first, third, n-m)
```bash
sed 1;3;n,md {file}
```

### Sort

Sort a CSV file by 2nd, 1st and 3rd columns.
```bash
sort --field-separator=',' --key=2,1,3 {file}
```

### Group By

### View a CSV file on the command line

You can just do a simple cat:

```bash
cat somefile.csv
```

For a nice view with proper spacing:

```bash
column -s, -t -n < somefile.csv | less -#2 -N -S
```

Note: when you have empty fields, you will need to put some kind of placeholder in it, otherwise the column gets merged with following columns. The following example demonstrates how to use sed to insert a placeholder:

```console
$ cat data.csv
1,2,3,4,5
1,,,,5
$ sed 's/,,/, ,/g;s/,,/, ,/g' data.csv | column -s, -t -n
1  2  3  4  5
1           5
$ cat data.csv
1,2,3,4,5
1,,,,5
$ column -s, -t -n < data.csv
1  2  3  4  5
1  5
$ sed 's/,,/, ,/g;s/,,/, ,/g' data.csv | column -s, -t -n
1  2  3  4  5
1           5
```
