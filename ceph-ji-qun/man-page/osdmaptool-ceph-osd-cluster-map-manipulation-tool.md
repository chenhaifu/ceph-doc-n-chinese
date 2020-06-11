# OSDMAPTOOL – CEPH OSD CLUSTER MAP MANIPULATION TOOL

## OSDMAPTOOL – CEPH OSD CLUSTER MAP MANIPULATION TOOL

### SYNOPSIS

**osdmaptool** _mapfilename_ \[–print\] \[–createsimple _numosd_ \[–pgbits _bitsperosd_ \] \] \[–clobber\]**osdmaptool** _mapfilename_ \[–import-crush _crushmap_\]**osdmaptool** _mapfilename_ \[–export-crush _crushmap_\]**osdmaptool** _mapfilename_ \[–upmap _file_\] \[–upmap-max _max-optimizations_\] \[–upmap-deviation _max-deviation_\] \[–upmap-pool _poolname_\] \[–upmap-save _file_\] \[–upmap-save _newosdmap_\] \[–upmap-active\]**osdmaptool** _mapfilename_ \[–upmap-cleanup\] \[–upmap-save _newosdmap_\]

### DESCRIPTION

**osdmaptool** is a utility that lets you create, view, and manipulate OSD cluster maps from the Ceph distributed storage system. Notably, it lets you extract the embedded CRUSH map or import a new CRUSH map. It can also simulate the upmap balancer mode so you can get a sense of what is needed to balance your PGs.

### OPTIONS

`--print`

will simply make the tool print a plaintext dump of the map, after any modifications are made.`--dump <format>`

displays the map in plain text when &lt;format&gt; is ‘plain’, ‘json’ if specified format is not supported. This is an alternative to the print option.`--clobber`

will allow osdmaptool to overwrite mapfilename if changes are made.`--import-crush mapfile`

will load the CRUSH map from mapfile and embed it in the OSD map.`--export-crush mapfile`

will extract the CRUSH map from the OSD map and write it to mapfile.`--createsimple numosd [--pg-bits bitsperosd] [--pgp-bits bits]`

will create a relatively generic OSD map with the numosd devices. If –pg-bits is specified, the initial placement group counts will be set with bitsperosd bits per OSD. That is, the pg\_num map attribute will be set to numosd shifted by bitsperosd. If –pgp-bits is specified, then the pgp\_num map attribute will be set to numosd shifted by bits.`--create-from-conf`

creates an osd map with default configurations.`--test-map-pgs [--pool poolid] [--range-first <first> --range-last <last>]`

will print out the mappings from placement groups to OSDs. If range is specified, then it iterates from first to last in the directory specified by argument to osdmaptool. Eg: **osdmaptool –test-map-pgs –range-first 0 –range-last 2 osdmap\_dir**. This will iterate through the files named 0,1,2 in osdmap\_dir.`--test-map-pgs-dump [--pool poolid] [--range-first <first> --range-last <last>]`

will print out the summary of all placement groups and the mappings from them to the mapped OSDs. If range is specified, then it iterates from first to last in the directory specified by argument to osdmaptool. Eg: **osdmaptool –test-map-pgs-dump –range-first 0 –range-last 2 osdmap\_dir**. This will iterate through the files named 0,1,2 in osdmap\_dir.`--test-map-pgs-dump-all [--pool poolid] [--range-first <first> --range-last <last>]`

will print out the summary of all placement groups and the mappings from them to all the OSDs. If range is specified, then it iterates from first to last in the directory specified by argument to osdmaptool. Eg: **osdmaptool –test-map-pgs-dump-all –range-first 0 –range-last 2 osdmap\_dir**. This will iterate through the files named 0,1,2 in osdmap\_dir.`--test-random`

does a random mapping of placement groups to the OSDs.`--test-map-pg <pgid>`

map a particular placement group\(specified by pgid\) to the OSDs.`--test-map-object <objectname> [--pool <poolid>]`

map a particular placement group\(specified by objectname\) to the OSDs.`--test-crush [--range-first <first> --range-last <last>]`

map placement groups to acting OSDs. If range is specified, then it iterates from first to last in the directory specified by argument to osdmaptool. Eg: **osdmaptool –test-crush –range-first 0 –range-last 2 osdmap\_dir**. This will iterate through the files named 0,1,2 in osdmap\_dir.`--mark-up-in`

mark osds up and in \(but do not persist\).`--mark-out`

mark an osd as out \(but do not persist\)`--tree`

Displays a hierarchical tree of the map.`--clear-temp`

clears pg\_temp and primary\_temp variables.`--health`

dump health checks`--with-default-pool`

include default pool when creating map`--upmap-cleanup <file>`

clean up pg\_upmap\[\_items\] entries, writing commands to &lt;file&gt; \[default: - for stdout\]`--upmap <file>`

calculate pg upmap entries to balance pg layout writing commands to &lt;file&gt; \[default: - for stdout\]`--upmap-max <max-optimizations>`

set max upmap entries to calculate \[default: 10\]`--upmap-deviation <max-deviation>`

max deviation from target \[default: 5\]`--upmap-pool <poolname>`

restrict upmap balancing to 1 pool or the option can be repeated for multiple pools`--upmap-save`

write modified OSDMap with upmap changes`--upmap-active`

Act like an active balancer, keep applying changes until balanced

### EXAMPLE

To create a simple map with 16 devices:

```text
osdmaptool --createsimple 16 osdmap --clobber
```

To view the result:

```text
osdmaptool --print osdmap
```

To view the mappings of placement groups for pool 1:

```text
osdmaptool osdmap --test-map-pgs-dump --pool 1

pool 0 pg_num 8
1.0     [0,2,1] 0
1.1     [2,0,1] 2
1.2     [0,1,2] 0
1.3     [2,0,1] 2
1.4     [0,2,1] 0
1.5     [0,2,1] 0
1.6     [0,1,2] 0
1.7     [1,0,2] 1
#osd    count   first   primary c wt    wt
osd.0   8       5       5       1       1
osd.1   8       1       1       1       1
osd.2   8       2       2       1       1
 in 3
 avg 8 stddev 0 (0x) (expected 2.3094 0.288675x))
 min osd.0 8
 max osd.0 8
size 0  0
size 1  0
size 2  0
size 3  8
```

In which,

1. pool 1 has 8 placement groups. And two tables follow:
2. A table for placement groups. Each row presents a placement group. With columns of:
   * placement group id,
   * acting set, and
   * primary OSD.
3. A table for all OSDs. Each row presents an OSD. With columns of:
   * count of placement groups being mapped to this OSD,
   * count of placement groups where this OSD is the first one in their acting sets,
   * count of placement groups where this OSD is the primary of them,
   * the CRUSH weight of this OSD, and
   * the weight of this OSD.
4. Looking at the number of placement groups held by 3 OSDs. We have
   * avarge, stddev, stddev/average, expected stddev, expected stddev / average
   * min and max
5. The number of placement groups mapping to n OSDs. In this case, all 8 placement groups are mapping to 3 different OSDs.

In a less-balanced cluster, we could have following output for the statistics of placement group distribution, whose standard deviation is 1.41421:

```text
     #osd    count   first   primary c wt    wt
     osd.0   8       5       5       1       1
     osd.1   8       1       1       1       1
     osd.2   8       2       2       1       1

     #osd    count   first    primary c wt    wt
     osd.0   33      9        9       0.0145874     1
     osd.1   34      14       14      0.0145874     1
     osd.2   31      7        7       0.0145874     1
     osd.3   31      13       13      0.0145874     1
     osd.4   30      14       14      0.0145874     1
     osd.5   33      7        7       0.0145874     1
      in 6
      avg 32 stddev 1.41421 (0.0441942x) (expected 5.16398 0.161374x))
      min osd.4 30
      max osd.1 34
     size 00
     size 10
     size 20
     size 364

To simulate the active balancer in upmap mode::

     osdmaptool --upmap upmaps.out --upmap-active --upmap-deviation 6 --upmap-max 11 osdmap

osdmaptool: osdmap file 'osdmap'
writing upmap command output to: upmaps.out
checking for upmap cleanups
upmap, max-count 11, max deviation 6
pools movies photos metadata data
prepared 11/11 changes
Time elapsed 0.00310404 secs
pools movies photos metadata data
prepared 11/11 changes
Time elapsed 0.00283402 secs
pools data metadata movies photos
prepared 11/11 changes
Time elapsed 0.003122 secs
pools photos metadata data movies
prepared 11/11 changes
Time elapsed 0.00324372 secs
pools movies metadata data photos
prepared 1/11 changes
Time elapsed 0.00222609 secs
pools data movies photos metadata
prepared 0/11 changes
Time elapsed 0.00209916 secs
Unable to find further optimization, or distribution is already perfect
osd.0 pgs 41
osd.1 pgs 42
osd.2 pgs 42
osd.3 pgs 41
osd.4 pgs 46
osd.5 pgs 39
osd.6 pgs 39
osd.7 pgs 43
osd.8 pgs 41
osd.9 pgs 46
osd.10 pgs 46
osd.11 pgs 46
osd.12 pgs 46
osd.13 pgs 41
osd.14 pgs 40
osd.15 pgs 40
osd.16 pgs 39
osd.17 pgs 46
osd.18 pgs 46
osd.19 pgs 39
osd.20 pgs 42
Total time elapsed 0.0167765 secs, 5 rounds
```

### AVAILABILITY

**osdmaptool** is part of Ceph, a massively scalable, open-source, distributed storage system. Please refer to the Ceph documentation at [http://ceph.com/docs](http://ceph.com/docs) for more information.

### SEE ALSO

[ceph](https://docs.ceph.com/docs/nautilus/man/8/ceph/)\(8\), [crushtool](https://docs.ceph.com/docs/nautilus/man/8/crushtool/)\(8\),
