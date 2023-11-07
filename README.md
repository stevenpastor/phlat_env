# phlat_env
PHLAT HLA allotyping

## SINGULARITY installation on CHOP's Respublica

* Load Singularity:

```
module load singularity
```

* Download/copy these files to your CWD and untag phlat:

```
cp -rf /mnt/isilon/maris_lab/target_nbl_ngs/steven/shared_files/index4phlat .
cp -f /mnt/isilon/maris_lab/target_nbl_ngs/steven/shared_files/phlat_1.0.tar.gz .
tar zxf phlat_1.0.tar.gz

# for testing below:
cp -f /mnt/isilon/maris_lab/target_nbl_ngs/steven/shared_files/subset.fastq .

# I have it all in one dir right now:
mkdir -p files_to_transfer
mv -f index4phlat files_to_transfer/index4phlat
mv -f subset.fastq files_to_transfer/
```

* Pull the dockerhub image (will generate phlat_env.sif in CWD):

```
singularity pull phlat_env.sif docker://spastors/phlat_env:latest
```

* Run example command to see if works:

```
singularity exec phlat_env.sif python2 -O /usr/bin/phlat-release/dist/PHLAT.py -h
```

* Run it for real (note that only using 1 thread below, so change that):

```
singularity run --containall --bind /mnt/isilon/maris_lab/target_nbl_ngs/steven/projects/immunopeptidomics/testing/files_to_transfer:/usr/files_to_transfer phlat_env.sif python2 -O /usr/bin/phlat-release/dist/PHLAT.py -1 /usr/files_to_transfer/subset.fastq -index /usr/files_to_transfer/index4phlat -b2url /usr/bin/bowtie2 -tag "test_tag" -e /usr/bin/phlat-release -o /usr/files_to_transfer -p 1
```

* __NOTE:__ if you want to run your own fastq and index files, just change the binds but do not change the binds on the container side:

```
# i.e., can change this one:
/mnt/isilon/maris_lab/target_nbl_ngs/steven/projects/immunopeptidomics/testing/files_to_transfer

# but do not change this one:
/usr/files_to_transfer
```

---

## CONDA installation on CHOP's Respublica

* assumes conda installed

* if on Respublica, copy these files over to CWD (else Slack/email me for them, as they are necessary):

```
cp -rf /mnt/isilon/maris_lab/target_nbl_ngs/steven/shared_files/index4phlat .
cp -f /mnt/isilon/maris_lab/target_nbl_ngs/steven/shared_files/phlat_1.0.tar.gz .
tar zxf phlat_1.0.tar.gz
# for a test:
cp -f /mnt/isilon/maris_lab/target_nbl_ngs/steven/shared_files/subset.fastq .
```

* create python2.7 env and activate it:

```
conda create -n py27 python=2.7 -y
conda activate py27
```

* PHLAT requires specific pysam and samtools:

```
pip install pysam==0.8.4
conda install -c bioconda samtools=1.14 -y
```

* also requires specific bowtie2 (easier to download manually):

```
wget https://anaconda.org/bioconda/bowtie2/2.2.4/download/linux-64/bowtie2-2.2.4-py27_1.tar.bz2
tar xjf bowtie2-2.2.4-py27_1.tar.bz2 
rm bowtie2-2.2.4-py27_1.tar.bz2
# above creates bin dir and info dir…can change the names or clean it up just note that the example command further below will need to be edited to reflect those new dirs
```

* test dataset:

```
mkdir -p test_out

# make sure the following exist, else have to edit the command below (note that if no directory in front of the file, assumes in CWD):
# phlat_1.0/dist/PHLAT.py
# subset.fastq
# index4phlat
# bin/bowtie2
# phlat_1.0
# test_out

# running with 1 thread (-p 1) and named test_tag:
python phlat_1.0/dist/PHLAT.py -1 subset.fastq -index index4phlat -b2url bin/bowtie2 -tag "test_tag" -e phlat_1.0/ -o test_out -p 1
```

