# hse22_hw1
## Корнев Андрей, группа 1

## Обязательная часть

#### Создадим ссылки на каждый из файлов

```bash
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq

```

#### Выберем случайные чтения, random seed = 624


```bash
seqtk sample -s624 oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s624 oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s624 oilMP_S4_L001_R1_001.fastq 1500000 > R1_matepairs.fastq
seqtk sample -s624 oilMP_S4_L001_R2_001.fastq 1500000 > R2_matepairs.fastq
```

#### С помощью FastQC оценим качество исходных чтений

```bash
mkdir fastqc
ls sub* *matepairs* | xargs -tI{} fastqc -o fastqc {}
```

#### С помощью MultiQC получим по ним общую статистику

```bash
mkdir multiqc
multiqc -o multiqc fastqc
```

#### С помощью программ platanus_trim и platanus_internal_trim подрежем чтения по качеству и удалим адаптеры

```bash
platanus_trim sub* 
platanus_internal_trim *matepairs*
```

#### Удалим исходные .fastq файлы

```bash
rm sub1.fastq
rm sub2.fastq
rm R1_matepairs.fastq
rm R2_matepairs.fastq
```

#### С помощью FastQC оценим качество обрезанных чтений и получим по ним статистику

```bash
mkdir trimmed_fastqc
ls *.trimmed | xargs -tI{} fastqc -o trimmed_fastqc {}
mkdir trimmed_multiqc
multiqc -o trimmed_multiqc trimmed_fastqc
```

#### С помощью программы “platanus assemble” соберем контиги из подрезанных чтений

```bash
time platanus assemble -o Poil -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log
```

#### С помощью программы “platanus scaffold” соберем скаффолды из контигов, а также из подрезанных чтений

```bash
time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 R1_matepairs.fastq.int_trimmed  R2_matepairs.fastq.int_trimmed 2> scaffold.log
```

#### С помощью программы “platanus gap_close” уменьшим кол-во гэпов с помощью подрезанных чтений

```bash
time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 R1_matepairs.fastq.int_trimmed R2_matepairs.fastq.int_trimmed 2> gapclose.log
```

#### Удалим .fastq файлы, содержащие подрезанные чтения:

```bash
rm *trimmed
```

## Отчёты multiQC
#### Для исходных чтений
![](https://github.com/Akorrred/hse22_hw1/blob/main/images/multiQC.png)

#### Для обрезанных чтений
![](https://github.com/Akorrred/hse22_hw1/blob/main/images/multiQC_trimmed.png)


## Работа в Jupyter Notebook

#### Контиги:

![](https://github.com/Akorrred/hse22_hw1/blob/main/images/Снимок%20экрана%202022-10-04%20в%2023.47.36.png)

#### Скаффолды:

![](https://github.com/Akorrred/hse22_hw1/blob/main/images/Снимок%20экрана%202022-10-04%20в%2023.47.49.png)

#### Гэпы в самом длинном скаффолде:

![](https://github.com/Akorrred/hse22_hw1/blob/main/images/Снимок%20экрана%202022-10-04%20в%2023.47.58.png)

#### Гэпы в самом длинном скаффолде после уменьшения количества гэпов:

![](https://github.com/Akorrred/hse22_hw1/blob/main/images/Снимок%20экрана%202022-10-04%20в%2023.48.14.png)
![](https://github.com/Akorrred/hse22_hw1/blob/main/images/Снимок%20экрана%202022-10-04%20в%2023.48.23.png)





