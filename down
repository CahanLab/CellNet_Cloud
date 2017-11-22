#!/usr/bin/env python

from __future__ import division
import random
import argparse
import os

parser = argparse.ArgumentParser()
parser.add_argument("input", help="input FASTQ Directory")
parser.add_argument("-n", "--number", type=int, help="number of reads to sample")
args = parser.parse_args()

random.seed(12)

if not args.number:
    print("No sample size specified. Defaulting to five million reads.")
    args.number = 5000000

# CREATE OUTPUT DIRECTORY
output_dir = "subset_"+args.input
os.mkdir(output_dir)
# LIST FILES TO BE DOWN-SAMPLED
fastq_files = os.listdir(args.input)

for fastq in fastq_files:

    print("\tcounting records....")
    with open(args.input+"/"+fastq) as inRead:
        num_lines = sum([1 for line in inRead])
    if int(num_lines % 4) != 0:
        print("FILE CORRUPTED: Number of lines in FASTQ file not divisible by 4. Is file decompressed?")
        exit()
    total_records = int(num_lines / 4)

    number_to_sample = args.number

    print("\tsampling " + str(number_to_sample) + " out of " + str(total_records) + " records")

    try:
        records_to_keep = set(random.sample(range(total_records), number_to_sample))
        record_number = 0
        with open(args.input+"/"+fastq) as inFile:
            with open(output_dir+"/"+"subset_"+fastq, "w") as output:
                for tag in inFile:
                    bases = next(inFile)
                    sign = next(inFile)
                    quality = next(inFile)
                    if record_number in records_to_keep:
                        output.write(tag)
                        output.write(bases)
                        output.write(sign)
                        output.write(quality)
                    record_number += 1
    except ValueError as e:
        if str(e) == "Sample larger than population or is negative":
            print("Desired number of reads is greater than number of reads in original file.")
            print("No down-sampling is necessary.")
        elif str(e) == "sample larger than population":
            print("Desired number of reads is greater than number of reads in original file.")
            print("No down-sampling is necessary.")
        else:
            raise

print("Compressing downsampled reads")
os.system("COPYFILE_DISABLE=1 tar cvfz compressed_reads.tgz "+output_dir)

if os.path.getsize("compressed_reads.tgz") >= 4000000000:
    print("WARNING: Your archive contains too many FASTQ files. Max size is 4GB.")
else:
    print("Archive file size is ~"+str(os.path.getsize("compressed_reads.tgz")/1000000000)+"GB")
