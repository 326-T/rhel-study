#　 shell script

## if

```shell
#!/bin/bash

for i in $(seq 1 100); do
  if [ $((i % 3)) -eq 0 ] && [ $((i % 5)) -eq 0 ]; then
    echo "FizzBuzz"
  elif [ $((i % 3)) -eq 0 ]; then
    echo "Fizz"
  elif [ $((i % 5)) -eq 0 ]; then
    echo "Buzz"
  else
    echo $i
  fi
done
```

## while

```shell
#!/bin/bash

count=0

while [ $count -lt 5 ]; do
  echo "count: $count"
  ((count++))
done
```

```shell
#!/bin/bash

while true; do
  read -p "何かキーを押してください（qで終了）: " input
  if [ "$input" == "q" ]; then
    break
  fi
done
```
