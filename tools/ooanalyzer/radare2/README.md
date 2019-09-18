# ooa2r2
A script to convert OOAnalyzer's output JSON file to Radare2 script for importing OOAnalyzer's Object-oriented information as metadata to Radare2

## Usage
```
ooa2r2.py [-h] (-j JSON_FILE) -o OUT_FILE [-liu | -dcn]

arguments:
  -h, --help            show this help message and exit
  -j JSON_FILE, --JSONFile JSON_FILE
                        Path to the OOAnalyzer's output JSON file
  -o OUT_FILE, --OutputFile OUT_FILE
                        Export to a specified file path(ex: OutputFileName.r2)
  -liu, --leave-import-usage-info   Don't export OOAnalyzer's usage info to Radare2
  -dcn, --use-demangled-class-names   Will try to use demangled class names(if available) while initializing classes
  ```
