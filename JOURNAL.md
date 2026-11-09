## Unit 7 — Issue selection

**Issue link:** [Issue #69: Output parser crashes on a top-level JSON array fallback](https://github.com/codepath/pathreview-ai201-fa26-howard/issues/69)

**Issue title:** Output parser crashes on a top-level JSON array fallback

**Tier:** [X] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
The PathReview RAG output parser (`rag/generator/output_parser.py`) is responsible for converting raw LLM text responses into structured `FeedbackSection` objects by attempting to parse valid JSON before falling back to plain text. Currently, when an LLM returns a top-level JSON array instead of a JSON object (such as a list of feedback strings), `json.loads()` successfully deserializes the string into a Python `list` and passes it directly to `_parse_json_output()`. Because `_parse_json_output()` assumes a dictionary structure and unconditionally executes `for key, value in data.items():`, it attempts to call `.items()` on a list instead of an object, raising an unhandled `AttributeError: 'list' object has no attribute 'items'` that crashes the parser rather than falling back gracefully. A successful fix will update the parser to check whether `data` is a list and appropriately transform array items into `FeedbackSection` objects without raising an exception. This will make the review generation pipeline resilient to array responses and allow the covering test `test_json_array_fallback` in `tests/unit/test_output_parser.py` to pass cleanly once its xfail marker is removed.

**Selection reasoning & scope fit:**
I selected **Tier 1** (Issue #69, tagged `tier-1`, `good first issue`, `rag`, `bug`) because it matches my current experience level and comfort navigating the PathReview codebase. As a newcomer to this architecture, I wanted an issue that is tightly scoped to a single self-contained component—specifically `rag/generator/output_parser.py`—rather than one requiring alterations across multi-stage retrieval workflows, database models, or external service dependencies. The problem represents a concrete and reproducible data structure typing defect (calling dictionary methods on a list), and an existing test in `tests/unit/test_output_parser.py` (`test_json_array_fallback`) already documents the intended behavior and provides unambiguous acceptance criteria. This makes it an ideal fit for building end-to-end confidence with the repository's development lifecycle, test harness (`pytest`), and contribution standards before taking on larger Tier 2 and Tier 3 enhancements.

**Branch name:** `fix/69-json-array-fallback`

**Setup confirmation:** [X] App runs locally at localhost:5173

**Cohort ledger:** [X] Issue added to cohort ledger
