# Lab Report 3

This report is intended to show different options for `grep`. The work will be
based on the `written_2` directory found on the [ANC Website][1].

## How to Get Documentation

Although this report covers some command-line options for `grep`, there are
many more that are not covered. If you want to look further into the
documentation, you can either run the following command in terminal:

```shell
man grep
```

or read the [GNU manual][2] or [Linux manual page][3]. For this report, any
information will be taken from the GNU manual

## Limiting the Number of Reads with `-m`

According to the [GNU manual][4], `-m num` will read the first *num* selected
lines. When `grep` stops, it outputs any trailing context lines. For example,
let's take the following command with the following output

```shell
$ grep "Italy" written_2/travel_guides/berlitz1/HistoryItaly.txt
        Italy has only existed as a nation since 1871. Before then,
        and Roman communities in Italy, but know very little of the country’s
        in Macedonia, Asia Minor, Spain, and southern France. The rest of Italy
        influence of their culture in Italy. Romans infused Greek refinement
        and fall, Italy took a back seat as the realm of power moved with the
        century a.d. , Christianity spread from Rome through southern Italy,
        Emperor Justinian (527–565) and his wife Theodora reannexed Italy to
        system. Under Heraclius (610–641), Greek was extended to Italy as its
        power with the Lombards (a Germanic tribe), who had invaded Italy in
        independent duchies. Lombard territory split Byzantine Italy up into
        political authority over all of Italy. Seeking the powerful support of
        Arab control of Sicily and southern Italy. Exploiting a natural genius
        brought great prosperity to Italy’s port cities. Pisa sided with the
        communes growing into full-fledged city-states, Italy was clearly not
        The Middle Ages in Italy were far from being the murky era
        and the French in Naples, southern Italy remained solidly feudal. Its
        it a rinascita, or rebirth, of the glories of Italy’s Greco-Roman past.
        French turned Italy into a battleground for the Kingdom of Naples and
        Italy’s Counter-Reformation. In alliance with the Jesuits, he weeded
        taken over northern Italy from the Spanish. The Age of Enlightenment
        Austrian sphere of influence, Italy remained solidly conservative.
        appeared on the scene, destined to lead the movement to a united Italy.
        family. If Napoleon did not exactly “liberate” Italy, he did shake up
        States of central Italy. But the Austrians defeated a rebel government
        Italia (Young Italy) movement sought national unity by popular-based
        Emanuele, who was proclaimed King of Italy. National unity was
        Italy took its place among modern nations as an unexceptional
        Both the politically left and the right wanted Italy to
        prestige. Socialists talked of Italy’s “civilizing mission” in the
        forces of capital and labor, Italy began its 20th century in a blithe
        Austria and Germany, Italy remained neutral in 1914. The following
        be “sacro egoismo,” Italy signed a secret treaty to enter the war on
        Italy’s national religion with guaranteed religious education in the
        country’s 57,000 Jews. The next year, Italy invaded Albania and, after
        The sordid hardships of post-war Italy — unemployment, the
        condescension about Italy’s technological and managerial talents. In
        the European Union, Italy has more than held its own in heavy industry,
        Sadly enough, as Italy put on its best face for the
        •600s Lombards invade Milan and much of Italy; Venice
        •1796–1814Napoleon invades north then much of Italy
        •1861Kingdom of Italy created; interim capitals of Turin
        •1871Rome named capital of unified Italy
        •1915Italy joins British/French/Russians in World War
        •1940Italy joins Nazi Germany in World War II
```

By adding `-m 10` to the command, we can instead limit our output to show the
first 10 instances of "Italy" as seen below:

```shell
$ grep "Italy" -m 10 written_2/travel_guides/berlitz1/HistoryItaly.txt
        Italy has only existed as a nation since 1871. Before then,
        and Roman communities in Italy, but know very little of the country’s
        in Macedonia, Asia Minor, Spain, and southern France. The rest of Italy
        influence of their culture in Italy. Romans infused Greek refinement
        and fall, Italy took a back seat as the realm of power moved with the
        century a.d. , Christianity spread from Rome through southern Italy,
        Emperor Justinian (527–565) and his wife Theodora reannexed Italy to
        system. Under Heraclius (610–641), Greek was extended to Italy as its
        power with the Lombards (a Germanic tribe), who had invaded Italy in
        independent duchies. Lombard territory split Byzantine Italy up into
```

This option can be useful is trying to find a specific string in the text that
appears multiple times. Furthermore, this option can be applied to many
other strings or counts. In the example below, `grep` now limits its search
for "Greek" in `HistoryGreek.txt` to the first 5 instances of it.

```shell
$ grep "Greek" -m 5 written_2/travel_guides/berlitz1/HistoryGreek.txt
        b.c. ), who had a base in the Peloponnese region of the Greek mainland.
        northern Greek coast, but the Phoenicians kept control of the main sea
        city-states began to grow in influence on the southern Greek mainland.
        Greek period. However, Greece was not yet a country; each city-state
        such as Carthage, a Greek city on the African coast of the
```

This option can also be useful if the user is trying to remove all instances
of a string within a file. If there are too many instances, using `-m num`
reduces the amount of information being printed to the terminal.

For more information, visit:
<https://www.gnu.org/software/grep/manual/grep.html#index-_002dm>.

## Recursively Searching Files with `-r`

According to the [GNU manual][5], `-r` will recursively search all the files
in a directory recursively. For example, let's say we know the word "Monasterio
de Las Descalzas Reales" exists somewhere in the `written_2` directory, but
aren't sure which file it could be in. In the example below, we'll see `-r`
being used to recursively search through each file in `written_2` in order to
find the String.

```shell
$ grep "Monasterio de Las Descalzas Reales" -r written_2
written_2/travel_guides/berlitz1/WhereToMadrid.txt:        Convento de la
Incarnación) is the Monasterio de Las Descalzas Reales
```

From here, we can see that our expected string is in `WhereToMadrid.txt`. While
a user could instead search each file one by one to determine its location,
this wouldn't be practical in a directory with hundreds of files and
subdirectories. Furthermore, this example can be useful to determine which
files within a directory contain a certain string. For example:

```shell
$ grep "Inca" -rl written_2
written_2/travel_guides/berlitz1/WhereToJapan.txt
written_2/travel_guides/berlitz1/WhereToMallorca.txt
written_2/travel_guides/berlitz1/WhatToMallorca.txt
written_2/travel_guides/berlitz1/WhereToMadrid.txt
```

By using `-r`, we are able to determine that "Inca" appears somewhere within
the four files shown. (Note that `-l` is used to simplify the output and will
be explained later.) Overall, the `-r` option is useful when you want `grep`
to search through all files that are contained within a directory.

For more information, visit:
<https://www.gnu.org/software/grep/manual/grep.html#index-_002dr>

## Limit Output to Files with `-l`

According to the [GNU manual][6], `-l` replaces the normal output (the text
surrounding a line) with the path to the file instead. On its own, this option
is not very useful. When combined with `-r`, `-l` helps reduce the amount
of information presented to our output. For example, let's say we were trying
to find files that mentioned "Mexicans" anywhere in the text. If we just used
`grep -r`, then our output would also include a lot of the text surrounding
the word. However, using `-l` in conjunction with `-r` gives us the following:

```shell
$ grep "Mexicans" -lr written_2
written_2/non-fiction/OUP/Castro/chR.txt
written_2/non-fiction/OUP/Castro/chP.txt
written_2/non-fiction/OUP/Castro/chB.txt
written_2/non-fiction/OUP/Castro/chC.txt
written_2/non-fiction/OUP/Castro/chA.txt
written_2/non-fiction/OUP/Castro/chV.txt
written_2/non-fiction/OUP/Castro/chM.txt
written_2/non-fiction/OUP/Castro/chZ.txt
written_2/non-fiction/OUP/Castro/chL.txt
written_2/non-fiction/OUP/Castro/chN.txt
written_2/travel_guides/berlitz2/Cancun-WhatToDo.txt
written_2/travel_guides/berlitz2/California-History.txt
written_2/travel_guides/berlitz2/Cancun-History.txt
written_2/travel_guides/berlitz2/Vallarta-WhatToDo.txt
```

Now, we know that the word "Mexicans" is mentioned in any of these files. This
makes it easier for us to look through the files we may want to consider.
Similarly, if we wanted to instead search for "tyranny" within our files, we
could replace the string and apply `-l` to determine which files it is
mentioned in.

```shell
$ grep "tyranny" -lr written_2
written_2/travel_guides/berlitz1/HistoryJamaica.txt
written_2/travel_guides/berlitz1/WhereToFrance.txt
written_2/travel_guides/berlitz2/Boston-WhereToGo.txt
```

Overall, `-l` is useful at reducing the output of `grep` to file names when
you're trying to search for where a specific word is mentioned in many
different files.

For more information, visit:
<https://www.gnu.org/software/grep/manual/grep.html#index-_002dl>.

## Finding Line Number with `-n`

According to the [GNU manual][7], `-n` prefixes each line of output with its
corresponding line number. This is useful when you're trying to search for the
line that contains the specific word. Let's look at one of the files from a
previous example, `WhereToFrance.txt`. Since the file has 3679 lines, we
wouldn't want to search for "tyranny" by hand. However, we can run the
following command to see:

```shell
$ grep "tyranny" -n written_2/travel_guides/berlitz1/WhereToFrance.txt
1900:        of resistance to tyranny. Gustave Flaubert was born in Rouen and
used
```

We now know to look at line 1900 to determine the context of "tyranny",
something that would have taken a while by just searching line by line.
Similarly, we can use it to find where multiple instances of a word occur in a
file as seen below:

```shell
$ grep "Mexicans" -n written_2/travel_guides/berlitz2/Vallarta-WhatToDo.txt
9:Mexico is known for its countless festivities and perennial spirit of
celebration — Mexicans may have even invented the “art of the party.” In
Mexico, it’s a given that any reason is a reason to celebrate, and traditional
fiestas will include the entire family, with plenty of food and drink,
contributed by all those involved.
71:In Puerto Escondido and Huatulco, you’ll find an abundance of tequila’s
close cousin, mezcal. Mezcal is distilled from the juice of more common types
of maguey plants found in the central region of Mexico. It has a stronger taste
and smell than tequila. Oaxacan mezcal is the type that is bottled with a
maguey worm pickled in the bottom of the bottle. To the ancient Mexicans, the
maguey plant was considered sacred, and the fact that the worm had ingested a
part of the “spirit” of this revered plant made consuming them both a delicacy
and a way to obtain good luck. Salud!
```

As we can see, the `-n` option is useful when trying to determine which lines
a certain string occurs on in a file.

For more information, visit:
<https://www.gnu.org/software/grep/manual/grep.html#index-_002dn>.

[1]: https://anc.org/data/oanc/download/
[2]: https://www.gnu.org/software/grep/manual/grep.html
[3]: https://man7.org/linux/man-pages/man1/grep.1.html
[4]: https://www.gnu.org/software/grep/manual/grep.html#index-_002dm
[5]: https://www.gnu.org/software/grep/manual/grep.html#index-_002dr
[6]: https://www.gnu.org/software/grep/manual/grep.html#index-_002dl
[7]: https://www.gnu.org/software/grep/manual/grep.html#index-_002dn
