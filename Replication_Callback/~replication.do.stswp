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


* --------------------------
* Stage 3.1: Descriptive statistics
* --------------------------
* * Treatment: race == "b" (Black-sounding name)
* Control: race == "w" (White-sounding name)
summ call if race == "b"
scalar rate_treat = r(mean)
scalar n_treat = r(N)

summ call if race == "w"
scalar rate_ctrl = r(mean)
scalar n_ctrl = r(N)

scalar point_diff = rate_treat - rate_ctrl
scalar rel_diff = (rate_treat - rate_ctrl) / rate_ctrl

disp "==== Callback by race ===="
disp "Treatment (b, Black-sounding): N = " n_treat ", callback rate = " rate_treat
disp "Control   (w, White-sounding): N = " n_ctrl ", callback rate = " rate_ctrl
disp "Point difference (pp): " point_diff * 100
disp "Relative difference (%): " rel_diff * 100
disp "Note: Point difference = percentage points; Relative difference = proportional change, NOT percentage points."

disp "Interpretation: Black-named resumes have 3.2 percentage points lower callback rate, which is 33.2% lower relative to white callback rate."

* --------------------------
* Stage3.2: Replicate Table1
* --------------------------

summ call if city == "c" & race == "w"
summ call if city == "b" & race == "w"
summ call if sex == "f" & race == "w"
summ call if sex == "f" & kind =="a" & race == "w"
summ call if sex == "f" & kind =="s" & race == "w"
summ call if sex == "m" & race == "w"

summ call if city == "c" & race == "b"
summ call if city == "b" & race == "b"
summ call if sex == "f" & race == "b"
summ call if sex == "f" & kind =="a" & race == "b"
summ call if sex == "f" & kind =="s" & race == "b"
summ call if sex == "m" & race == "b"

* ---- 1.1 All sent resumes ----
summ call if race == "w"
local all_w_r = string(r(mean)*100, "%9.2f")
local all_w_n = "[" + string(r(N), "%9.0fc") + "]"
summ call if race == "b"
local all_b_r = string(r(mean)*100, "%9.2f")
local all_b_n = "[" + string(r(N), "%9.0fc") + "]"
capture prtest call, by(race)
local all_p   = "(" + string(r(p), "%9.4f") + ")"
local all_rat = string(`all_w_r' / `all_b_r', "%9.2f")
local all_dif = string(`all_w_r' - `all_b_r', "%9.2f")

* ---- 1.2 Chicago ----
summ call if city == "c" & race == "w"
local chi_w_r = string(r(mean)*100, "%9.2f")
local chi_w_n = "[" + string(r(N), "%9.0fc") + "]"
summ call if city == "c" & race == "b"
local chi_b_r = string(r(mean)*100, "%9.2f")
local chi_b_n = "[" + string(r(N), "%9.0fc") + "]"
capture prtest call if city == "c", by(race)
local chi_p   = "(" + string(r(p), "%9.4f") + ")"
local chi_rat = string(`chi_w_r' / `chi_b_r', "%9.2f")
local chi_dif = string(`chi_w_r' - `chi_b_r', "%9.2f")

* ---- 1.3 Boston ----
summ call if city == "b" & race == "w"
local bos_w_r = string(r(mean)*100, "%9.2f")
local bos_w_n = "[" + string(r(N), "%9.0fc") + "]"
summ call if city == "b" & race == "b"
local bos_b_r = string(r(mean)*100, "%9.2f")
local bos_b_n = "[" + string(r(N), "%9.0fc") + "]"
capture prtest call if city == "b", by(race)
local bos_p   = "(" + string(r(p), "%9.4f") + ")"
local bos_rat = string(`bos_w_r' / `bos_b_r', "%9.2f")
local bos_dif = string(`bos_w_r' - `bos_b_r', "%9.2f")

* ---- 1.4 Females ----
summ call if sex == "f" & race == "w"
local fem_w_r = string(r(mean)*100, "%9.2f")
local fem_w_n = "[" + string(r(N), "%9.0fc") + "]"
summ call if sex == "f" & race == "b"
local fem_b_r = string(r(mean)*100, "%9.2f")
local fem_b_n = "[" + string(r(N), "%9.0fc") + "]"
capture prtest call if sex == "f", by(race)
local fem_p   = "(" + string(r(p), "%9.4f") + ")"
local fem_rat = string(`fem_w_r' / `fem_b_r', "%9.2f")
local fem_dif = string(`fem_w_r' - `fem_b_r', "%9.2f")

* ---- 1.5 Females in administrative jobs ----
summ call if sex == "f" & kind == "a" & race == "w"
local fad_w_r = string(r(mean)*100, "%9.2f")
local fad_w_n = "[" + string(r(N), "%9.0fc") + "]"
summ call if sex == "f" & kind == "a" & race == "b"
local fad_b_r = string(r(mean)*100, "%9.2f")
local fad_b_n = "[" + string(r(N), "%9.0fc") + "]"
capture prtest call if sex == "f" & kind == "a", by(race)
local fad_p   = "(" + string(r(p), "%9.4f") + ")"
local fad_rat = string(`fad_w_r' / `fad_b_r', "%9.2f")
local fad_dif = string(`fad_w_r' - `fad_b_r', "%9.2f")

* ---- 1.6 Females in sales jobs ----
summ call if sex == "f" & kind == "s" & race == "w"
local fsa_w_r = string(r(mean)*100, "%9.2f")
local fsa_w_n = "[" + string(r(N), "%9.0fc") + "]"
summ call if sex == "f" & kind == "s" & race == "b"
local fsa_b_r = string(r(mean)*100, "%9.2f")
local fsa_b_n = "[" + string(r(N), "%9.0fc") + "]"
capture prtest call if sex == "f" & kind == "s", by(race)
local fsa_p   = "(" + string(r(p), "%9.4f") + ")"
local fsa_rat = string(`fsa_w_r' / `fsa_b_r', "%9.2f")
local fsa_dif = string(`fsa_w_r' - `fsa_b_r', "%9.2f")

* ---- 1.7 Males ----
summ call if sex == "m" & race == "w"
local mal_w_r = string(r(mean)*100, "%9.2f")
local mal_w_n = "[" + string(r(N), "%9.0fc") + "]"
summ call if sex == "m" & race == "b"
local mal_b_r = string(r(mean)*100, "%9.2f")
local mal_b_n = "[" + string(r(N), "%9.0fc") + "]"
capture prtest call if sex == "m", by(race)
local mal_p   = "(" + string(r(p), "%9.4f") + ")"
local mal_rat = string(`mal_w_r' / `mal_b_r', "%9.2f")
local mal_dif = string(`mal_w_r' - `mal_b_r', "%9.2f")


putdocx clear
putdocx begin
putdocx paragraph, style(Title)
putdocx text ("Callback Rate Analysis by Race")

putdocx paragraph
putdocx table master = (15, 5), border(all, nil) border(top, single) border(bottom, single)


putdocx table master(1,1) = ("Sample")
putdocx table master(1,2) = ("Percent callback" + char(10) + "for White names")
putdocx table master(1,3) = ("Percent callback for" + char(10) + "African-American names")
putdocx table master(1,4) = ("Ratio")
putdocx table master(1,5) = ("Percent difference" + char(10) + "(p-value)")

putdocx table master(1,.), bold

* --- Row 2-3: All sent resumes ---
putdocx table master(2,1) = ("All sent resumes")
putdocx table master(2,2) = ("`all_w_r'")
putdocx table master(3,2) = ("`all_w_n'")
putdocx table master(2,3) = ("`all_b_r'")
putdocx table master(3,3) = ("`all_b_n'")
putdocx table master(2,4) = ("`all_rat'")
putdocx table master(2,5) = ("`all_dif'")
putdocx table master(3,5) = ("`all_p'")

* --- Row 4-5: Chicago ---
putdocx table master(4,1) = ("Chicago")
putdocx table master(4,2) = ("`chi_w_r'")
putdocx table master(5,2) = ("`chi_w_n'")
putdocx table master(4,3) = ("`chi_b_r'")
putdocx table master(5,3) = ("`chi_b_n'")
putdocx table master(4,4) = ("`chi_rat'")
putdocx table master(4,5) = ("`chi_dif'")
putdocx table master(5,5) = ("`chi_p'")

* --- Row 6-7: Boston ---
putdocx table master(6,1) = ("Boston")
putdocx table master(6,2) = ("`bos_w_r'")
putdocx table master(7,2) = ("`bos_w_n'")
putdocx table master(6,3) = ("`bos_b_r'")
putdocx table master(7,3) = ("`bos_b_n'")
putdocx table master(6,4) = ("`bos_rat'")
putdocx table master(6,5) = ("`bos_dif'")
putdocx table master(7,5) = ("`bos_p'")

* --- Row 8-9: Females ---
putdocx table master(8,1) = ("Females")
putdocx table master(8,2) = ("`fem_w_r'")
putdocx table master(9,2) = ("`fem_w_n'")
putdocx table master(8,3) = ("`fem_b_r'")
putdocx table master(9,3) = ("`fem_b_n'")
putdocx table master(8,4) = ("`fem_rat'")
putdocx table master(8,5) = ("`fem_dif'")
putdocx table master(9,5) = ("`fem_p'")

* --- Row 10-11: Females in administrative jobs ---
putdocx table master(10,1) = ("Females in administrative jobs")
putdocx table master(10,2) = ("`fad_w_r'")
putdocx table master(11,2) = ("`fad_w_n'")
putdocx table master(10,3) = ("`fad_b_r'")
putdocx table master(11,3) = ("`fad_b_n'")
putdocx table master(10,4) = ("`fad_rat'")
putdocx table master(10,5) = ("`fad_dif'")
putdocx table master(11,5) = ("`fad_p'")

* --- Row 12-13: Females in sales jobs ---
putdocx table master(12,1) = ("Females in sales jobs")
putdocx table master(12,2) = ("`fsa_w_r'")
putdocx table master(13,2) = ("`fsa_w_n'")
putdocx table master(12,3) = ("`fsa_b_r'")
putdocx table master(13,3) = ("`fsa_b_n'")
putdocx table master(12,4) = ("`fsa_rat'")
putdocx table master(12,5) = ("`fsa_dif'")
putdocx table master(13,5) = ("`fsa_p'")

* --- Row 14-15: Males ---
putdocx table master(14,1) = ("Males")
putdocx table master(14,2) = ("`mal_w_r'")
putdocx table master(15,2) = ("`mal_w_n'")
putdocx table master(14,3) = ("`mal_b_r'")
putdocx table master(15,3) = ("`mal_b_n'")
putdocx table master(14,4) = ("`mal_rat'")
putdocx table master(14,5) = ("`mal_dif'")
putdocx table master(15,5) = ("`mal_p'")

capture erase "$output/descriptive_results.docx"
putdocx save "$output/descriptive_results.docx", replace

* --------------------------
* Stage 4: Estimate the baseline effect
* --------------------------
* create dummy for white-sounding name
gen white = (race == "w")

* OLS: linear probability model (LPM)
eststo m1: reg call white, robust

* show group means manually, verify beta = mean(call|white=1) - mean(call|white=0)
summ call if white == 1
summ call if white == 0


* --------------------------
* Stage 5: Add precision controls
* --------------------------
encode city, gen(city_num)
encode sex, gen(sex_num)
eststo m2: reg call white i.city_num i.sex_num i.h i.education years_exp computer_skills special_skills, robust




