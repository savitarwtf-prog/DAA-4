# Assignment 4. Patient Zero

Student ID:253970
Name:Zhurek Aldiyar
Group:se-2540

This file is the report. Fill it in as you go. How to start: `START_HERE.md`. The task: `docs/Assignment4.pdf`.

## Program output

The 9 lines your program prints on your data (`java -cp out Main`):
Mutations: 69
Shared core: 918
Marker hits: 6
First hit: 14644
Border length: 4
Shared words: 414
Tests needed: 32
Cheapest testing: 154
Longest chain: 31

My outbreak report (a screenshot of `output/outbreak.html`):

![Uploading image.png…]()


## Questions

Mission 1. d[i][j] is the smallest number of changes (replace, insert, delete) that turn the first i letters of A into the first j letters of B. It is computed from three smaller cells: d[i-1][j-1] (replace, cost 0 if the letters are equal, else 1), d[i-1][j]+1 (delete) and d[i][j-1]+1 (insert); I take the minimum. The first row and column are j and i, because the other word is empty.

Mission 2. A substring must be continuous, a subsequence may skip letters, only the order must stay. My Shared core is bigger than the number of letters that are the same at the same place, because after an insert or a delete the letters shift, and a subsequence can still match them in the same order.

Mission 3. The prefix function pi[i] is the length of the longest proper prefix of marker[0..i] that is also its suffix. When a mismatch happens, KMP does not go back in the text: the part already matched is known, and pi tells how much of it is still a valid prefix of the marker, so only the pointer in the marker moves back. My Border length is 4: the first 4 letters of my marker are the same as its last 4 letters, so two occurrences can overlap by 4 letters.

Mission 4. Different windows can have the same hash (a collision), so equal hashes do not prove equal words; I must compare the letters. If many windows have the same hash, every window needs a letter-by-letter comparison and the time grows from O(n+m) towards O(n*m).

Mission 5. For every person v: in[v] is the cheapest cost of the subtree of v when v is tested, out[v] when v is not tested. in[v] = cost[v] + sum of min(in[c], out[c]) over the children c; out[v] = sum of in[c], because if v is not tested, every child must be tested. The answer is min(in[0], out[0]). Greedy "most links first" can fail, because a person with many links may be very expensive, and testing his cheap neighbours instead gives a smaller total cost.

## Timing (Mission 7)

Milliseconds, average of 3 runs, from `java -cp out Bench`.

| test | size | time |
|---|---|---|
| naive search | 20 000 / 40 000 / 80 000 | 44.2 / 81.4 / 168.6 |
| KMP | 20 000 / 40 000 / 80 000 | 1.8 / 0.9 / 0.3 |
| edit distance | 500 / 1000 / 2000 / 4000 | 4.7 / 8.0 / 18.5 / ... |

The naive search grows about two times when the text is doubled (44.2 -> 81.4 -> 168.6 ms), because it makes about n*m comparisons and the pattern length m is fixed. KMP is tiny and does not grow (1.8 -> 0.9 -> 0.3 ms): it makes O(n+m) steps, and the pointer in the text never goes back, so the difference to the naive search becomes bigger for longer texts. Edit distance fills a table of n*m cells, so the time must grow about 4 times when the length is doubled; for small sizes it grows less (4.7 -> 8.0 -> 18.5 ms), because the Java start-up time is a big part of such short runs, and the growth comes closer to 4 times for length 4000.

## Complexity

n is the length of the text (or of a strain), m the length of the pattern, k the length of the window, v the number of people. Fill in every algorithm you wrote.

| algorithm | time | space | assumptions |
|---|---|---|---|
| Edit distance | O(n*m) | O(n*m) | full table d[n+1][m+1]; n, m are the lengths of the strains |
| LCS | O(n*m) | O(n*m) | full table, same size as edit distance |
| Prefix function and KMP | O(n+m) | O(n+m) | prefix function O(m); the search O(n) because the text pointer never goes back; space: the pi array (m) and the array of hits (up to n) |
| Rabin-Karp (shared words) | O(n+m) expected, O(n*m*k) worst case | O(m) | rolling hash is O(1) per window; the letters are compared only when hashes are equal; the worst case is when very many windows have the same hash |
| Tree DP | O(v) | O(v) | every person and every link is visited once (BFS and one pass over the order); arrays in[], out[], parent[] |

## Before you submit

- The four files in `data` and the screenshot `output/outbreak.png` are in the repository.
- `java tools/Check.java` shows all examples right.
- This README is filled in, the program output and the section AI use included.
- The work is pushed to main (at least one commit per mission) and the repository is public.
- The link is attached in Moodle.
#
