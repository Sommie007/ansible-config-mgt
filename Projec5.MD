# SHELL SCRIPTING
 ## **This project shows how to implement SHELL SCRIPTING**

[Setup an Ec2 instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html)

 Update a list of packages in package manager

 **`sudo apt update -y`**

 Open your termial, create a folder called shell-scripting using this command 

 **`mkdir shell-scripting`**

Create a file called script.sh in the shell-scripting directory using this command

**`touch script.sh`**

use the nano or vim command to edit the file and then copy into the file these block of code below
```
#!/bin/bash

#Prompt the user for their name
echo "Enter your name:"
read name

#Display a greeting with the entered name

echo "Hello, $name! Nice to meet you."
```
![Alt text](<Shell Scripting/script.png>)

then save your file

Run the command **`sudo chmod +x script.sh`**

Then run the script using this command **`./script.sh`**

![Alt text](<Shell Scripting/result from script.png>)

## **DIRECTORY MANIPULATION AND NAVIGATION**

Create a file called nagivating-linuxfile-system.sh shell-scripting directory using this command

**`touch nagivating-linuxfile-system.sh`**

use the nano or vim command to edit the file and then copy into the file these block of code below
```
#!/bin/bash

#Display current directory
echo "Current directory: $PWD"

#Create a new directory
echo "Creating a new directory..."
mkdir my_directory
echo "New directory created."

#Change to the new directory
echo "Changing to the new directory..."
cd my_directory
echo "Current directory: $PWD"

#Create some files
echo "Creating files..."
touch file1.txt
touch file2.txt
echo "Files created."

#List the files in the current directory
echo "Files in the current directory:"
ls

#Move one level up
echo "Moving one level up..."
cd ..
echo "Current directory: $PWD"

#Remove the new directory and its contents
echo "Removing the new directory..."
rm -rf my_directory
echo "Directory removed."

#List the files in the current directory again
echo "Files in the current directory:"
ls
```
![Alt text](<Shell Scripting/nagivating-linuxfile-system.png>)

Run the command **`sudo chmod +x nagivating-linuxfile-system.sh`**

Then run the script using this command **`./nagivating-linuxfile-system.sh`**

![Alt text](<Shell Scripting/result from nagivating-linuxfile-system.png>)

## **FILE OPERATIONS AND SORTING**

Open your terminal and create a file called sorting.sh in the shell-scripting directory using this command

**`touch sorting.sh`**

use the nano or vim command to edit the file and then copy into the file these block of code below
```
#!/bin/bash

# Create three files
echo "Creating files..."
echo "This is file3." > file3.txt
echo "This is file1." > file1.txt
echo "This is file2." > file2.txt
echo "Files created."

# Display the files in their current order
echo "Files in their current order:"
ls

# Sort the files alphabetically
echo "Sorting files alphabetically..."
ls | sort > sorted_files.txt
echo "Files sorted."

# Display the sorted files
echo "Sorted files:"
cat sorted_files.txt

# Remove the original files
echo "Removing original files..."
rm file1.txt file2.txt file3.txt
echo "Original files removed."

# Rename the sorted file to a more descriptive name
echo "Renaming sorted file..."
mv sorted_files.txt sorted_files_sorted_alphabetically.txt
echo "File renamed."

# Display the final sorted file
echo "Final sorted file:"
cat sorted_files_sorted_alphabetically.txt

```
![Alt text](<Shell Scripting/sorting.png>)

then save your file

Run the command **`sudo chmod +x sorting.sh`**

Then run the script using this command **`./sorting.sh`**

![Alt text](<Shell Scripting/result from sorting.png>)

## **WORKING WITH NUMBER AND CALCULATIONS**

Open your terminal and create a file called calculations.sh in the shell-scripting directory using this command

**`touch calculations.sh`**

use the nano or vim command to edit the file and then copy into the file these block of code below
```
#!/bin/bash

# Define two variables with numeric values
num1=10
num2=5

# Perform basic arithmetic operations
sum=$((num1 + num2))
difference=$((num1 - num2))
product=$((num1 * num2))
quotient=$((num1 / num2))
remainder=$((num1 % num2))

# Display the results
echo "Number 1: $num1"
echo "Number 2: $num2"
echo "Sum: $sum"
echo "Difference: $difference"
echo "Product: $product"
echo "Quotient: $quotient"
echo "Remainder: $remainder"

# Perform some more complex calculations
power_of_2=$((num1 ** 2))
square_root=$(awk "BEGIN{ sqrt=$num2; print sqrt }")

# Display the results
echo "Number 1 raised to the power of 2: $power_of_2"
echo "Square root of number 2: $square_root"
```
![Alt text](<Shell Scripting/calculations.png>)

then save your file

Run the command **`sudo chmod +x calculations.sh`**

Then run the script using this command **`./calculations.sh`**

![Alt text](<Shell Scripting/result from calculation.png>)

## **WORKING WITH NUMBER AND CALCULATIONS**

Open your terminal and create a file called backup.sh in the shell-scripting directory using this command

**`touch backup.sh`**

use the nano or vim command to edit the file and then copy into the file these block of code below
```
#!/bin/bash

# Define the source directory and backup directory
source_dir="/path/to/source_directory"
backup_dir="/path/to/backup_directory"

# Create a timestamp with the current date and time
timestamp=$(date +"%Y%m%d%H%M%S")

# Create a backup directory with the timestamp
backup_dir_with_timestamp="$backup_dir/backup_$timestamp"

# Create the backup directory
mkdir -p "$backup_dir_with_timestamp"

# Copy all files from the source directory to the backup directory
cp -r "$source_dir"/* "$backup_dir_with_timestamp"

# Display a message indicating the backup process is complete
echo "Backup completed. Files copied to: $backup_dir_with_timestamp"
```
![Alt text](<Shell Scripting/backup.png>)

then save your file

Run the command **`sudo chmod +x backup.sh`**

Then run the script using this command **`./backup.sh`**

![Alt text](<Shell Scripting/result from backup.png>)

**THANK YOU**