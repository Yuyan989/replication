clear all
version 18


* --------------------------
* Define relative path globals
* "." refers to the current working directory: Replication_Callback
* --------------------------
global root "."
global raw    "$root/raw"
global process "$root/process"
global output "$root/output"

* Create subfolders automatically if missing
foreach folder in raw process output {
    capture mkdir "$root/`folder'"
}

* --------------------------
* 1.Import raw csv data
* --------------------------
import delimited "$raw/labor_market_discrimination.csv", clear

* Save processed dataset
save "$process/labor_discrimination_processed.dta", replace
