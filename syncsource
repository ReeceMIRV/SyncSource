#!/bin/bash

# Title: SyncSource
# Description: Synchronize between a source directory & a destination directory
# Author: Reece
# Created: November 27, 2023

source='/Users/reece/Library/CloudStorage/GoogleDrive-reyesm@carleton.edu/My Drive'
destination='/Users/reece/Documents/Drive'

helpPage() {
	echo $'\nSyncSource is a scrypt that aims at synchronizing between a source directory & a destination directory'
	echo '----'
	echo 'The following directories are used as the source & destination directories if no arguments are given:'
	echo '    Source: $source'
	echo '    Destination: $destination'
	echo
	echo 'Syntax'
	echo $'    ./SyncSource.sh\n'
	echo $'    ./SyncSource.sh <sourceDir> <destinationDir>\n'
}

invalidInput() {
	echo $'Invalid flag: Please use the -h flag for assistance in using SyncSource\n'
}

# Create flags/options
while getopts 'h' flag; do
	case $flag in
		h) # Handle the -h, -help flag
		# Display script help information
			helpPage
			;;
   		\?)
   		# Handle invalid options
			invalidInput
			;;
	esac
done

if [ "$1" ] && [ "$2" ]; then
	source="$1"
	destination="$2"
fi

# Check if the source directory exists. If it doesn't, then create it.
if [ ! -d "$source" ]; then
	echo "Source directory: $source does not exist."
	echo "Creating source directory..."
	{
	#try
		mkdir "$source"
	} || {
	#catch
		echo "Failed to create source directory"
		exit 1
	}
fi

# Check if the destination directory exists. If it doesn't, then create it.
if [ ! -d "$destination" ]; then
	echo "Destination directory: $destination does not exist."
	echo "Creating destination directory..."
	{
	#try
		mkdir "$destination"
	} || {
	#catch
		echo "Failed to create destination directory..."
		exit 1
	}
fi

### Compare the source files to the destination files and copy over the new/newer files ###

# Find all files & directories, accounting for file names & directories that contain spaces
find "$source" -print0 | while read -d $'\0' file; do
	# Exclude the source path from the file path ${parameter/pattern/string}
	localFilePath="${file/$source\//}"

	# If the file path is the source or destination path, then ignore it
	if [ "$file" == "$source" ] || [ "$file" == "$destination" ]; then
		:
	# If the file does not exist (meaning that the destination path does not contain the same file as the source path), then copy the file over
	elif [ ! -e "$destination/$localFilePath" ]; then
		echo "$destination/$localFilePath does not exist. Copying..."
		cp -r "$source/$localFilePath" "$destination/$localFilePath"
	# Otherwise if the file does exist then...
	elif [ -f "$destination/$localFilePath" ]; then
		# Check to see -> If the source file is newer than the destination file, if so then... update it
		if [ "$source/$localFilePath" -nt "$destination/$localFilePath" ]; then
			echo "$destination/$localFilePath is not up to date. Updating file..."
			cp -r "$source/$localFilePath" "$destination/$localFilePath"
		fi
	fi
done

### Compare the destination files to the source files and remove any files that are no longer in the source
find "$destination" -print0 | while read -d $'\0' file; do
	# Exclude the destination path from the file path ${parameter/pattern/string}
	localFilePath="${file/$destination\//}"

	# If the file path is the destination or source path, then ignore it
	if [ "$file" == "$destination" ] || [ "$file" == "$source" ]; then
		:
	# If the file does not exist in the source but exists in the destination then delete it
	elif [ ! -e "$source/$localFilePath" ] && [ -e "$destination/$localFilePath" ]; then
		echo "$destination/$localFilePath is not in source. Deleting..."
		rm -r "$destination/$localFilePath"
	fi
done

exit 0