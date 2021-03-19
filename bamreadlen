#!/usr/bin/env bash

# Calculate the sequencing read length from a BAM file.
# The answer is computed as the median read length from the
# first MAX_READS reads in the file.

# Authors: Bernie Pope, bjpope@unimelb.edu.au

# Dependencies: samtools, awk

# default number of reads from the start of the input BAM file to consider
max_reads=10000

program_name="bamreadlen"
bamfile=""

# Help message for using the program.
function show_help {

cat << UsageMessage
${program_name}: calculate the sequencing read length from a BAM file 
Usage:
    ${program_name} [-h] [-n max_reads] -i input_bam_file_name 

   -i <input_bam_file_name> is required, this specifies the input BAM file name

   -n <max_reads>           is optional, if specified this is the maximum
                            number of reads to consider from the file

   -h                       displays an help message
                     
Example:
    ${program_name} -i example.bam 
Dependencies:
   The following tools must be installed on your computer to use this script,
   and be accessible via the PATH environment variable:
   - bash 
   - awk 
   - samtools 
UsageMessage
}


# echo an error message $1 and exit with status $2
function exit_with_error {
    error_message=$1
    exit_status_value=$2
    printf "${program_name}: ERROR: ${error_message}\n"
    exit ${exit_status_value}
}


# Parse the command line arguments and set the global variables language and new_project_name
function parse_args {
    local OPTIND opt

    while getopts "hi:n:" opt; do
        case "${opt}" in
            h)
                show_help
                exit 0
                ;;
            i)  bamfile="${OPTARG}"
                ;;
            n)  max_reads="${OPTARG}"
                ;;
        esac
    done

    shift $((OPTIND-1))

    [ "$1" = "--" ] && shift

    if [[ -z ${bamfile} ]]; then
		exit_with_error "missing command line argument: -i bamfile, use -h for help" 2
    fi
}

function check_dependencies {
    # Check for samtools 
    samtools --version > /dev/null || {
       exit_with_error "samtools is not installed in the PATH\nPlease install samtools, and ensure it can be found in your PATH variable." 1
    }
    # Check for awk 
    awk --version > /dev/null || {
       exit_with_error "awk is not installed in the PATH\nPlease install awk, and ensure it can be found in your PATH variable." 1
    }
}

# Check if the directory for the new project already exists.
function check_bamfile_exists {
    if test -r "${bamfile}" -a -f "${bamfile}"; then
        # Do nothing, file exists
        : 
    else
        exit_with_error "bam file ${bamfile} does not exist or is not readable." 1
    fi
}

# Compute the lengths of the first MAX_READS reads in the input BAM file.
# Sort them. Then return the middle value (median).
# This may not be the true median (when the number of reads considered is even),
# but it is close enough
function get_readlen {
    samtools view ${bamfile} | head -${max_reads} | awk '{print length($10)}' | sort -n | awk ' { a[i++]=$1; } END { print a[int(i/2)]; }'
}

# Parse command line arguments.
parse_args "$@"
# Check that dependencies are met
check_dependencies 
# Check that the bam file exists as is readable
check_bamfile_exists
# calculate read len
get_readlen

exit 0 
