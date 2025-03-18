# Log-Analysis-Tools-CLI
- In this repository there is a brief overview of the CLI tools used in log analysis
- While command-line log analysis offers powerful capabilities, it might only suit some scenarios, especially when dealing with vast and complex log datasets. A dedicated log analysis solution, like the Elastic 
 (ELK) Stack or Splunk, can be more efficient and offer additional log analysis and visualization features. However, the command line remains essential for quick and straightforward log analysis tasks.

## ➡️ cat 

The ```cat``` command (short for "concatenate") is a simple utility that reads one or more files and displays its content in the terminal. When used for log files, it prints the entire log content to the screen.

```
cat file.log
```

## ➡️ less

The ```less``` command is an improvement over ```cat``` when dealing with larger files. It allows you to view the file's data page by page, providing a more convenient way to read through lengthy logs. When using ```less``` to open a file, it displays the first page by default, and you can scroll down using the arrow keys or with Page Up and Page Down.

```
cat file.log
```

## ➡️ tail

The ```tail``` command is specifically designed for viewing the end of files and is very useful for seeing a summary of recently generated events in the case of log files. The most common use of ```tail``` is coupled with the ```-f``` option, which allows you to "follow" the log file in real-time, as it continuously updates the terminal with new log entries as they are generated and written. This is extremely useful when monitoring logs for live events or real-time system behavior.

```
tail -f file.log
```
## ➡️ wc

The ```wc``` (word count) command is a simple but powerful utility that can be quite useful for quick analysis and statistics gathering. The output of ```wc``` provides information about the number of lines, words, and characters in a log file. This can help security analysts understand the size and volume of log data they are dealing with before diving into a more detailed analysis.

```
wc file.log
```

## ➡️ cut

The ```cut``` command extracts specific columns (fields) from files based on specified delimiters. This is a handy command for working with log files that have structured or tab-separated data.

If we want to extract all of the IP addresses in the file, we can use the ```cut``` command to specify a delimiter of a ```space``` character and only select the first field returned.

```
cut -d ' ' -f 1 file.log
```
- This command will return a list of every IP address in the log file. Expanding on this, we can change the field number to ```-f 7``` to extract the ```URLs``` and ```-f 9``` to extract the ```HTTP status codes```.

## ➡️ sort

Sometimes, it's helpful to sort the returned entries chronologically or alphabetically. The ```sort``` command arranges the data in files in ascending or descending order based on specific criteria. This can be crucial for identifying patterns, trends, or outliers in our log data. It is also common to combine the output of another command ```cut``` and use it as the input of the sort command using the pipe ```|``` redirection character.

For example, to ```sort``` the list of returned IP addresses from the above ```cut``` command, we can run:

```
cut -d ' ' -f 1 file.log | sort -n
```
- In the above command, we ```piped``` the output from ```cut``` into the ```sort``` command and added the ```-n``` option to sort numerically. This changed the output to list the IP addresses in ascending order.
- If we want to reverse the order, we can add the ```-r``` option
```
cut -d ' ' -f 1 file.log | sort -n -r
```
## ➡️ uniq

The ```uniq``` command identifies and removes adjacent duplicate lines from sorted input. In the context of log analysis, this can be a useful tool for simplifying data lists (like collected IP addresses), especially when log entries may contain repeated or redundant information. The ```uniq``` command is often combined with the ```sort``` command to sort the data before removing the duplicate entries.

- For example, the output of the ```sort``` command we ran above contains a few duplicate IP addresses, which is easier to spot when the data is sorted numerically. To remove these repeatedly extracted IPs from the list, we can run:

```
cut -d ' ' -f 1 file.log | sort -n -r | uniq
```
- We can also append the ```-c``` option to output unique lines and prepend the count of occurrences for each line. This can be very useful for quickly determining IP addresses with unusually high traffic.
```
cut -d ' ' -f 1 file.log | sort -n -r | uniq -c
```

## ➡️ sed

Both ```sed``` and ```awk``` are powerful text-processing tools commonly used for log analysis. They are sometimes used interchangeably, but both commands have their use cases and can allow security analysts to manipulate, extract, and transform log data efficiently.

Using the substitute syntax, ```sed``` can replace specific patterns or strings into log entries. For example, to replace all occurrences of ```"31/Jul/2023"``` with ```"July 31, 2023"``` in the ```file.log``` file, we can use:
```
sed 's/31\/Jul\/2023/July 31, 2023/g' file.log
```
- Note that the backslash character ```\``` is required to ```"escape"``` the forward slash in our pattern and tell sed to treat the forward slash as a literal character. Also, note that the ```sed``` command does not change the ```file.log``` file directly; instead, it only outputs the modified version of the file to the standard output in the command line. If you want to overwrite the file, you can add the ```-i``` option to edit the file in place or use a redirect operator ```>``` to save the output to the original or another file.

- Caution: If you use the ```-i``` option with ```sed```, you risk overwriting the original file and losing valuable data. Ensure to keep a backup copy!

## ➡️ awk

For the ```awk``` command, a common use case, is conditional actions based on specific field values. For example, to print log entries where the ```HTTP response code``` is greater than or equal to ```400``` (which would indicate HTTP error statuses), we can use the following command:

```
awk '$9 >= 400' file.log
```
In this case, we're using the ```$9``` field (which in this log example refers to the HTTP status codes), requiring it to be greater than or equal to ```400```.

## ➡️ grep

The ```grep``` command is a powerful text search tool widely used on UNIX systems and provides exceptional use cases in log analysis. It allows you to search for specific patterns or regular expressions within files or streams of text. Using ```grep``` can help analysts quickly identify relevant log entries that match specific criteria, particular resources or keywords, or patterns associated with security incidents.

The most basic usage of ```grep``` is to search for specific strings within log files. For example, if we are suspicious about any log entries that hit the ```/admin.php``` webpage on the server, we can ```grep``` for ```"admin"``` to return any relevant results:

```
grep "admin" file.log
```
- Like the ```uniq -c``` command, we can append the ```-c``` option to grep to count the entries matching the search criteria. For example, because only a single line was returned in the above command, appending ```-c``` will return "1".
```
grep -c "admin" file.log
```
- If we wanted to know which line number in the log file relates to the matched entries, we could add the ```-n``` option to help quickly locate specific occurrences:
```
grep -n "admin" file.log
```
- Lastly, we can invert our command using the ```-v``` option only to select lines that do not contain the specified pattern or keyword(s). This can be useful for quickly filtering out unwanted or irrelevant lines from log files. For example, if we're not interested in any log entries that hit the ```/index.php``` page, we can run the following command to filter it out:
```
grep -v "/index.php" apache.log | grep "203.64.78.90"
```
- In the above command, we filtered out the ```index.php``` page and piped the output into another grep command that only pulled log entries that contained the IP address ```203.64.78.90```
  
