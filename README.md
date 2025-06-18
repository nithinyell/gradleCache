```
for file in Currency=*,*; do
  newname=$(echo "$file" | sed -E 's/^Currency=([A-Z]+),.*$/\1.png/')
  mv "$file" "$newname"
done

```
