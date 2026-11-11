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

* --------------------------
* 2.Data audit
* --------------------------
use "$process/labor_discrimination_processed.dta"
* 1. Check total rows and columns
describe
count

* 2. Check ad_id: NOT expected to be unique (multiple applications per ad)
duplicates report ad_id

* 3. Validate treatment race: only "b" / "w"
tab race, missing
levelsof race, clean local(race_check)

* 4. Validate outcome call: binary 0/1
tab call, missing
levelsof call, clean local(call_check)

* 5. Check h and l: mutually exclusive AND exhaustive
tab h l, missing
count if h == 1 & l == 1
count if h == 0 & l == 0

* 6. Check categorical controls
tab sex, missing
tab city, missing
tab kind, missing
tab eoe, missing

* 7. Check numeric resume attributes
summ education years_exp n_jobs honors volunteer military emp_holes email computer_skills special_skills

* 8. Note for text variables: do NOT replace missing with 0 blindly
capture which mdesc
if _rc ssc install mdesc
mdesc
tab exp_min_req, missing
tab school_req , missing
tab ownership, missing