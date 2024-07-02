# Gene tree w aa input file work flow

1. ## Rename codificant sequences

```javascript
#!/bin/bash

# Lista de archivos en el directorio
#files=("CONS_hembra.codingseq" "POS_hembra.codingseq" "PUC_hembra.codingseq" "CONS_macho.codingseq" "POS_macho.codingseq" "PUC_macho.codingseq")
# Lista de archivos en el directorio que contienen la palabra "short"
files=($(ls *short*.codingseq))
# Iterar sobre cada archivo
for file in "${files[@]}"; do
  # Crear un archivo temporal
  temp_file="${file}.tmp"
  
  # Leer el archivo línea por línea
  while read -r line; do
    if [[ $line == \>* ]]; then
      # Si la línea comienza con '>', agregar el nombre del archivo al encabezado del gen
      echo "${line}_${file}" >> "$temp_file"
    else
      # Si la línea no comienza con '>', copiarla tal cual
      echo "$line" >> "$temp_file"
    fi
  done < "$file"
  
  # Reemplazar el archivo original con el archivo temporal
  mv "$temp_file" "$file"
done

echo "Proceso completado."
```

2. ## Re extension file:

```javascript
for archivo in *.codingseq; do
mv "file" "{file%.txt}.fasta"
done
```

1. ## Find Orthologous with orthofinder

```other
conda activate orthofinder
orthofinder -d  -f ./ -t 40
```

   Results:

[Statistics_Overall.tsv](https://res.craft.do/user/full/40726e0e-eed0-d2d3-cdd3-891e95006266/doc/00f8b933-c6d4-439d-abca-9e34ed989fba/9131f255-fc80-4205-925d-ac12b630be35)

2. ## Alignment with mafft

```other
#!/bin/bash
mkdir -p /media/server/a77f75fe-fd07-402e-84d7-a7341c29141c/fertorres/mazz_cds/OrthoFinder/Results_Jul01/alineados

for input_file in /media/server/a77f75fe-fd07-402e-84d7-a7341c29141c/fertorres/mazz_cds/OrthoFinder/Results_Jul01/Single_Copy_Orthologue_Sequences/*.fa; do
    output_file="/media/server/a77f75fe-fd07-402e-84d7-a7341c29141c/fertorres/mazz_cds/OrthoFinder/Results_Jul01/alineados/$(basename "${input_file%.fa}_mafft.fa")"
    mafft "$input_file" > "$output_file"
done
```

```other
conda activate mafft
bashh alinea.sh
```

1. ## Filters
   1. ### Entropy-based Filtering with BMGE

```other
#!/bin/bash
mkdir -p /media/server/a77f75fe-fd07-402e-84d7-a7341c29141c/fertorres/mazz_cds/bmge

for input_file in /media/server/a77f75fe-fd07-402e-84d7-a7341c29141c/fertorres/mazz_cds/alineados/*.fa; do
    output_file="/media/server/a77f75fe-fd07-402e-84d7-a7341c29141c/fertorres/mazz_cds/bmge/$(basename "${input_file%.fa}_bmge.fa")"
    java -jar /home/server/Escritorio/Programas/BMGE/BMGE/src/BMGE.jar -i "$input_file" -t DNA -o "$output_file"
done
```

   2. ### Size Filtering with Geneious
   - Min sequence length: 300
   - % Pairwise Identiti: > 90%

   ## **Results in repository**

