# Search Console Issues - Resolved 2026-02-08

## Issue #1: pressurewashingatlanta.com - 403 Forbidden

**Root Cause:** Domain pointing to empty `/powerwashingatlanta/` directory, no redirect rule in .htaccess

**Fix:** Added 301 redirect rule in .htaccess → apluspowercleaning.com

**Verified:** ✅ HTTP 301 working

---

## Issue #2: apluspowercleaning.com - 5xx Server Errors

**Root Cause:** AIOSEO Pro plugin missing `seo_analyzer_scan_date` column in `wp_aioseo_terms` table. Caused repeated DB errors (44MB error_log!)

**Fix:** Added missing column via SQL ALTER statement

**Verified:** ✅ HTTP 200, DB structure corrected (50 columns total)

---

## What Happens Next
- Google re-crawls in 3-7 days
- Error log should stop growing
- Optional: Request re-validation in GSC to speed up

## Files Modified
- `/.htaccess` - domain redirect rule
- DB table `wp_aioseo_terms` - added missing column

## Source Emails
- gmail:19c3ead46beed2a3
- gmail:19c2ba960af0e630
- gmail:19c2ba092d0df4e2
- gmail:19c2bd0527a51dfd
