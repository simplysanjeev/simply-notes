# Database Indexing

## 💡 The Core Concept
An index in a database is exactly like the index at the back of a thick textbook. Instead of reading every single page to find a specific record (a "Full Table Scan"), the database looks at the index, finds the exact location on the disk, and jumps straight there.

## 🛠 How it Works (B-Tree)
By default, databases use a tree-like data structure called a B-Tree. When you search for a value, the database traverses this tree, checking if your target value is higher or lower than the current node. This allows the engine to instantly ignore massive chunks of the table, turning a search through millions of rows into just a few rapid jumps.

## ⚖️ The Golden Rule (Trade-offs)
You cannot index every column. Every index has a tangible architectural cost:
* **The PRO (Faster Reads):** Reading, searching, and filtering data becomes exponentially faster.
* **The CON (Slower Writes):** Every time you `INSERT`, `UPDATE`, or `DELETE` a row, the database has to update the actual table *and* reorganize the index tree. Heavy indexing destroys write throughput.
* **Storage Cost:** Indexes are redundant copies of your data and consume significant RAM and disk space.

**The Architect's Takeaway:** Only create indexes on columns that you filter by (`WHERE`), sort by (`ORDER BY`), or join on frequently (like `email` or `user_id`).