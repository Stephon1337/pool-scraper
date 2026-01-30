# Knowledge Base — Pool Service Tool Suite

## Traps & Lessons Learned

### 1. `git checkout` destroys uncommitted work
**Trigger:** Running `git checkout .` or `git restore .` without committing first
**Impact:** Lost entire session of changes (~900+ edits)
**Fix:** Always commit a checkpoint before any destructive git operation

### 2. Pre-commit hook blocks docs mentioning `api-token`
**Trigger:** Committing files containing the string `api-token` (even in documentation)
**Impact:** Commit rejected by `.git/hooks/pre-commit` secret detection
**Fix:** Unstage documentation files, or modify pre-commit to whitelist `.md` files

### 3. Discount text too long for circular badges
**Trigger:** Discount texts like "6 MONTH MOVING SPECIAL" or "BUY 2 ALGAE TREATMENT!"
**Impact:** Text overflows badge, gets cut off
**Fix:** Keep all discount texts under 12 characters. Use pool-specific short phrases.

### 4. Forced badge dimensions break back-side layouts
**Trigger:** Global CSS `.discount { width: 85px; height: 85px; }` applied to ALL badges
**Impact:** Back-side rectangular badges crushed to tiny squares
**Fix:** Scope size constraints to `.postcard-front .discount[style*="border-radius: 50%"]` only

### 5. `text-overflow: ellipsis` hides critical info
**Trigger:** Phone numbers, company names truncated with "..."
**Impact:** User can't see full phone number on postcard
**Fix:** Use `autoFitText()` to shrink font instead. Never use ellipsis on postcards.

### 6. Layout thrashing in badge resize
**Trigger:** Reading and writing DOM in same loop
**Impact:** Forced reflow on every iteration
**Fix:** Batch reads (Phase 1) then batch writes (Phase 2) pattern

### 7. Odd discount numbers look unprofessional
**Trigger:** Amounts like $37, $47, $97
**Impact:** User said "NO ODD NUMBERS"
**Fix:** Use only round amounts: $25, $50, $75, $100, $150, $200, percentages

### 8. Unbalanced layouts — content only on one side
**Trigger:** Headlines left-aligned in full-width containers
**Impact:** Right half of card appears empty
**Fix:** Center-align headline wrappers with `text-align: center`

## Architecture Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Keep all 268 layouts | Yes | User values uniqueness and variety over simplicity |
| Quick Mode | Added on top | One-click design for beginners, Advanced for power users |
| Dashboard | iframe-based | Each tool stays independent, no refactoring needed |
| Security | escapeHTML + sanitize* | XSS prevention across all 268 template literals |
| Text fitting | autoFitText() | Shrink font to fit, never truncate |
