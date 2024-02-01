```bash
------------------------------------------------------------------------------------------
 (sudo  du -a /var/log/ |sort -nr|head -n20 |awk '{print $NF}'|while read l ;do du -csh $l|grep -vi total;done ) 2> /dev/null

------------------------------------------------------------------------------------------
~/bin/lgr_files.sh  . 2
------------------------------------------------------------------------------------------

units() { awk -v pfix="$1" \
  'BEGIN { yect=6  # Array element-count
    split("Byt KiB MiB GiB TiB PiB",lbl)
    for (i=1;i<=yect;i++) { val[i] = (2**(10*(i-1)))-1 } 
  }
  { yess=yect  # Array element-subscript
    while ( $1 < val[yess] ){ yess-- }
    num = $1 / (val[yess]+1)
    sub(/^[0-9]*\t*/,"")
    if (yess!=1) { printf "%s %8.2f %s  %s\n", pfix, num, lbl[yess], $0 }
    else        { printf "%s %5d    %s  %s\n", pfix, num, lbl[yess], $0 }
   }'
}
tdir="/tmp/$USER/$(basename $0)"
[[ ! -d "$tdir" ]] && mkdir -p "$tdir"
file="$tdir/$(date +%N)"
echo "================"
dirs="$file.dirs";   du --max-depth=$2 -b $1  >"$dirs" ; <"$dirs"  sort -nr           | units "dir"
echo "================"
filz="$file.filz"; { du --max-depth=$2 -ab $1 ; cat "$dirs" ; } | sort -nr | uniq -u  | units " f "
echo "================"
rm   "$file."* 
------------------------------------------------------------------------------------------
```


----------------

password gen 

xkcdpass | sed -e "s/\b\(.\)/\u\1/g" | sed -e 's/ /_/g' | tr 'aeio' '4310'

awk '{for (i=1;i<=NF;i++) if ($i~/\./ && $i~"@") {gsub(/a/,"4",$i);gsub(/e/,"3",$i);gsub(/i/,"1",$i);gsub(/o/,"0",$i)}}1'


awk '
    {
    for (i=1;i<=NF;i++)             # Loop trough all fields in the string
        if ($i~/\./ && $i~"@") {    # If sting a field contains "." and "@" assume email
            gsub(/a/,"4",$i)        # Change the letter for the field
            gsub(/e/,"3",$i)        # Change the letter for the field
            gsub(/i/,"1",$i)        # Change the letter for the field
            gsub(/o/,"0",$i)        # Change the letter for the field
            }
    }1' file                        # Read the input file

    
