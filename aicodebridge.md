<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>DSA 30-Day Tracker</title>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;600&family=Syne:wght@400;700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0e0e10;
    --surface: #18181c;
    --surface2: #222228;
    --border: #2e2e38;
    --accent: #7c6af7;
    --accent2: #4fc8a0;
    --accent3: #f7a84a;
    --danger: #f76a6a;
    --text: #e8e8f0;
    --muted: #6b6b80;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: 'Syne', sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; padding: 0 0 4rem; }

  .header {
    background: var(--surface); border-bottom: 1px solid var(--border);
    padding: 1.2rem 2rem; position: sticky; top: 0; z-index: 100;
    display: flex; align-items: center; gap: 1.5rem; flex-wrap: wrap;
  }
  .header h1 { font-size: 1.2rem; font-weight: 800; letter-spacing: -0.02em; white-space: nowrap; }
  .header h1 span { color: var(--accent); }
  .progress-wrap { flex: 1; min-width: 160px; }
  .progress-bar-bg { background: var(--border); border-radius: 99px; height: 7px; overflow: hidden; }
  .progress-bar-fill { height: 100%; border-radius: 99px; background: linear-gradient(90deg, var(--accent), var(--accent2)); transition: width 0.4s cubic-bezier(.4,0,.2,1); }
  .progress-label { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--muted); margin-bottom: 5px; display: flex; justify-content: space-between; }
  .hdr-btns { display: flex; gap: 8px; flex-wrap: wrap; }
  .hbtn { font-family: 'JetBrains Mono', monospace; font-size: 11px; background: transparent; border: 1px solid var(--border); color: var(--muted); padding: 6px 12px; border-radius: 6px; cursor: pointer; transition: all 0.2s; white-space: nowrap; }
  .hbtn:hover { border-color: var(--accent); color: var(--accent); }
  .hbtn.danger:hover { border-color: var(--danger); color: var(--danger); }
  .hbtn.export { border-color: var(--accent2); color: var(--accent2); }

  .main { max-width: 920px; margin: 0 auto; padding: 2rem 1.5rem; }
  .stats { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; margin-bottom: 2.5rem; }
  .stat-card { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; padding: 1rem; text-align: center; }
  .stat-num { font-size: 2rem; font-weight: 800; font-family: 'JetBrains Mono', monospace; color: var(--accent); line-height: 1; }
  .stat-lbl { font-size: 11px; color: var(--muted); margin-top: 4px; text-transform: uppercase; letter-spacing: 0.06em; }

  .week { margin-bottom: 2.5rem; }
  .week-title { font-size: 11px; font-family: 'JetBrains Mono', monospace; text-transform: uppercase; letter-spacing: 0.1em; padding: 6px 12px; border-radius: 6px; display: inline-block; margin-bottom: 1rem; font-weight: 600; }
  .wk1 { background: rgba(59,130,246,0.15); color: #60a5fa; }
  .wk2 { background: rgba(34,197,94,0.15); color: #4ade80; }
  .wk3 { background: rgba(245,158,11,0.15); color: #fbbf24; }
  .wk4 { background: rgba(168,85,247,0.15); color: #c084fc; }
  .wk5 { background: rgba(247,106,106,0.15); color: #f76a6a; }

  .day-card { background: var(--surface); border: 1px solid var(--border); border-radius: 14px; margin-bottom: 10px; overflow: hidden; transition: border-color 0.2s; }
  .day-card.done { border-color: var(--accent2); }
  .day-card.done .day-header { background: rgba(79,200,160,0.06); }
  .day-card.practice-card { border-color: rgba(247,168,74,0.3); }
  .day-card.practice-card.done { border-color: var(--accent2); }
  .day-card.practice-card .day-header { background: rgba(247,168,74,0.04); }

  .day-header { padding: 14px 18px; display: flex; align-items: center; gap: 12px; cursor: pointer; user-select: none; transition: background 0.15s; }
  .day-header:hover { background: var(--surface2); }
  .day-num { font-family: 'JetBrains Mono', monospace; font-size: 10px; color: var(--muted); min-width: 36px; }
  .day-name { flex: 1; font-size: 14px; font-weight: 700; color: var(--text); }
  .day-name.done-text { color: var(--accent2); }

  .day-badge { font-size: 10px; font-family: 'JetBrains Mono', monospace; padding: 3px 9px; border-radius: 20px; white-space: nowrap; }
  .badge-easy  { background: rgba(34,197,94,0.15);  color: #4ade80; }
  .badge-med   { background: rgba(245,158,11,0.15); color: #fbbf24; }
  .badge-rev   { background: rgba(168,85,247,0.15); color: #c084fc; }
  .badge-mock  { background: rgba(247,106,106,0.15); color: #f76a6a; }
  .badge-practice { background: rgba(247,168,74,0.18); color: #f7a84a; }

  .chevron { color: var(--muted); font-size: 16px; transition: transform 0.25s; }
  .chevron.open { transform: rotate(90deg); }

  .topics { display: none; padding: 0 18px 16px 18px; border-top: 1px solid var(--border); }
  .topics.open { display: block; }

  .topic-item { display: flex; align-items: flex-start; gap: 12px; padding: 9px 0; border-bottom: 1px solid var(--border); }
  .topic-item:last-child { border-bottom: none; }
  .topic-item label { display: flex; align-items: flex-start; gap: 10px; cursor: pointer; flex: 1; font-size: 13px; color: var(--muted); line-height: 1.5; }
  .topic-item label.checked-label { text-decoration: line-through; opacity: 0.4; }

  /* LeetCode link items */
  .lc-item { display: flex; align-items: flex-start; gap: 12px; padding: 9px 0; border-bottom: 1px solid var(--border); }
  .lc-item:last-child { border-bottom: none; }
  .lc-item label { display: flex; align-items: flex-start; gap: 10px; cursor: pointer; flex: 1; font-size: 13px; color: var(--muted); line-height: 1.5; }
  .lc-item label.checked-label { text-decoration: line-through; opacity: 0.4; }
  .lc-item label.checked-label a { opacity: 0.4; }
  .lc-link { color: var(--accent3); text-decoration: none; font-size: 12px; font-family: 'JetBrains Mono', monospace; white-space: nowrap; margin-left: 6px; border-bottom: 1px dashed rgba(247,168,74,0.4); transition: color 0.15s; }
  .lc-link:hover { color: #fff; border-bottom-color: #fff; }
  .diff-easy { font-size: 10px; font-family: 'JetBrains Mono', monospace; background: rgba(34,197,94,0.12); color: #4ade80; padding: 1px 6px; border-radius: 4px; margin-left: 5px; }
  .diff-med  { font-size: 10px; font-family: 'JetBrains Mono', monospace; background: rgba(245,158,11,0.12); color: #fbbf24; padding: 1px 6px; border-radius: 4px; margin-left: 5px; }
  .lc-num { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--muted); min-width: 30px; }

  .practice-section-label { font-size: 10px; font-family: 'JetBrains Mono', monospace; color: var(--accent3); text-transform: uppercase; letter-spacing: 0.08em; padding: 10px 0 4px; opacity: 0.7; }

  input[type="checkbox"] {
    appearance: none; -webkit-appearance: none;
    width: 18px; height: 18px; min-width: 18px;
    border: 1.5px solid var(--border); border-radius: 5px;
    background: var(--surface2); cursor: pointer;
    position: relative; transition: all 0.15s; margin-top: 1px;
  }
  input[type="checkbox"]:hover { border-color: var(--accent); }
  input[type="checkbox"]:checked { background: var(--accent2); border-color: var(--accent2); }
  input[type="checkbox"]:checked::after {
    content: ''; position: absolute; left: 4px; top: 1px; width: 6px; height: 10px;
    border: 2px solid #0e0e10; border-top: none; border-left: none; transform: rotate(45deg);
  }

  .day-complete-pill { font-size: 10px; font-family: 'JetBrains Mono', monospace; background: rgba(79,200,160,0.15); color: var(--accent2); padding: 3px 10px; border-radius: 20px; }
  #import-file { display: none; }
  .toast { position: fixed; bottom: 2rem; left: 50%; transform: translateX(-50%); background: var(--surface2); border: 1px solid var(--accent2); color: var(--accent2); font-family: 'JetBrains Mono', monospace; font-size: 12px; padding: 10px 20px; border-radius: 8px; opacity: 0; transition: opacity 0.3s; pointer-events: none; z-index: 999; }
  .toast.show { opacity: 1; }

  @media (max-width: 600px) {
    .stats { grid-template-columns: repeat(2, 1fr); }
    .header { gap: 0.8rem; }
    .hdr-btns { width: 100%; }
  }
</style>
</head>
<body>

<div class="header">
  <h1>DSA <span>42</span> Tracker</h1>
  <div class="progress-wrap">
    <div class="progress-label"><span>Progress</span><span id="pct-label">0%</span></div>
    <div class="progress-bar-bg"><div class="progress-bar-fill" id="main-bar" style="width:0%"></div></div>
  </div>
  <div class="hdr-btns">
    <button class="hbtn export" onclick="exportProgress()">Export JSON</button>
    <button class="hbtn" onclick="document.getElementById('import-file').click()">Import JSON</button>
    <button class="hbtn danger" onclick="resetAll()">Reset</button>
  </div>
  <input type="file" id="import-file" accept=".json" onchange="importProgress(event)">
</div>

<div class="main">
  <div class="stats">
    <div class="stat-card"><div class="stat-num" id="s-done">0</div><div class="stat-lbl">Items Done</div></div>
    <div class="stat-card"><div class="stat-num" id="s-total">0</div><div class="stat-lbl">Total Items</div></div>
    <div class="stat-card"><div class="stat-num" id="s-days">0</div><div class="stat-lbl">Days Complete</div></div>
    <div class="stat-card"><div class="stat-num" id="s-left">0</div><div class="stat-lbl">Days Left</div></div>
  </div>
  <div id="plan"></div>
</div>

<div class="toast" id="toast"></div>

<script>
const STORAGE_KEY = 'dsa42_progress';

// topic item = plain string
// lc item = { lc: true, num, title, diff, url }
const plan = [
  { week: 1, wkLabel: "Week 1 — Arrays & Strings", wkClass: "wk1", days: [
    { day: 1, title: "Arrays Basics", badge: "badge-easy", badgeText: "Easy", topics: [
      "Traversal — loop through array, print elements",
      "Insert element at a given position",
      "Delete element from array",
      "Reverse an array in place",
      "Find maximum and minimum element",
      "Prefix sum array concept"
    ]},
    { day: 2, title: "Arrays — Patterns", badge: "badge-med", badgeText: "Medium", topics: [
      "Two pointers technique (opposite ends)",
      "Sliding window pattern (fixed & variable size)",
      "Kadane's algorithm — maximum subarray sum",
      "Understand when to use each pattern"
    ]},
    { day: 3, title: "Arrays Practice", badge: "badge-practice", badgeText: "Practice", isPractice: true, topics: [
      { lc:true, num:"1",   title:"Two Sum",                          diff:"Easy",   url:"https://leetcode.com/problems/two-sum/" },
      { lc:true, num:"26",  title:"Remove Duplicates from Sorted Array", diff:"Easy", url:"https://leetcode.com/problems/remove-duplicates-from-sorted-array/" },
      { lc:true, num:"121", title:"Best Time to Buy and Sell Stock",  diff:"Easy",   url:"https://leetcode.com/problems/best-time-to-buy-and-sell-stock/" },
      { lc:true, num:"53",  title:"Maximum Subarray (Kadane's)",      diff:"Medium", url:"https://leetcode.com/problems/maximum-subarray/" },
      { lc:true, num:"283", title:"Move Zeroes",                      diff:"Easy",   url:"https://leetcode.com/problems/move-zeroes/" },
      { lc:true, num:"167", title:"Two Sum II — Input Array Is Sorted",diff:"Medium",url:"https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/" },
      { lc:true, num:"209", title:"Minimum Size Subarray Sum",        diff:"Medium", url:"https://leetcode.com/problems/minimum-size-subarray-sum/" },
      { lc:true, num:"238", title:"Product of Array Except Self",     diff:"Medium", url:"https://leetcode.com/problems/product-of-array-except-self/" },
      { lc:true, num:"11",  title:"Container With Most Water",        diff:"Medium", url:"https://leetcode.com/problems/container-with-most-water/" },
      { lc:true, num:"15",  title:"3Sum",                             diff:"Medium", url:"https://leetcode.com/problems/3sum/" },
      { lc:true, num:"560", title:"Subarray Sum Equals K",            diff:"Medium", url:"https://leetcode.com/problems/subarray-sum-equals-k/" },
    ]},
    { day: 4, title: "Strings Basics", badge: "badge-easy", badgeText: "Easy", topics: [
      "Reverse a string",
      "Check if string is palindrome",
      "Check if two strings are anagrams",
      "Find substring in a string",
      "Count frequency of each character"
    ]},
    { day: 5, title: "Strings — Patterns", badge: "badge-med", badgeText: "Medium", topics: [
      "String manipulation tricks",
      "Frequency count using hashmap",
      "Naive pattern matching",
      "Sliding window on strings"
    ]},
    { day: 6, title: "Strings Practice", badge: "badge-practice", badgeText: "Practice", isPractice: true, topics: [
      { lc:true, num:"125", title:"Valid Palindrome",                  diff:"Easy",   url:"https://leetcode.com/problems/valid-palindrome/" },
      { lc:true, num:"242", title:"Valid Anagram",                     diff:"Easy",   url:"https://leetcode.com/problems/valid-anagram/" },
      { lc:true, num:"344", title:"Reverse String",                    diff:"Easy",   url:"https://leetcode.com/problems/reverse-string/" },
      { lc:true, num:"387", title:"First Unique Character in a String",diff:"Easy",   url:"https://leetcode.com/problems/first-unique-character-in-a-string/" },
      { lc:true, num:"14",  title:"Longest Common Prefix",             diff:"Easy",   url:"https://leetcode.com/problems/longest-common-prefix/" },
      { lc:true, num:"3",   title:"Longest Substring Without Repeating Characters", diff:"Medium", url:"https://leetcode.com/problems/longest-substring-without-repeating-characters/" },
      { lc:true, num:"567", title:"Permutation in String",             diff:"Medium", url:"https://leetcode.com/problems/permutation-in-string/" },
      { lc:true, num:"438", title:"Find All Anagrams in a String",     diff:"Medium", url:"https://leetcode.com/problems/find-all-anagrams-in-a-string/" },
      { lc:true, num:"49",  title:"Group Anagrams",                    diff:"Medium", url:"https://leetcode.com/problems/group-anagrams/" },
      { lc:true, num:"5",   title:"Longest Palindromic Substring",     diff:"Medium", url:"https://leetcode.com/problems/longest-palindromic-substring/" },
    ]},
    { day: 7, title: "Hashing", badge: "badge-med", badgeText: "Medium", topics: [
      "HashMap operations — put, get, remove",
      "HashSet for storing unique elements",
      "Frequency map pattern",
      "Two Sum problem using hashmap",
      "Count pairs with given sum"
    ]},
  ]},

  { week: 2, wkLabel: "Week 2 — Hashing, Math & Linked Lists", wkClass: "wk2", days: [
    { day: 8, title: "Hashing Practice", badge: "badge-practice", badgeText: "Practice", isPractice: true, topics: [
      { lc:true, num:"1",   title:"Two Sum",                           diff:"Easy",   url:"https://leetcode.com/problems/two-sum/" },
      { lc:true, num:"217", title:"Contains Duplicate",                diff:"Easy",   url:"https://leetcode.com/problems/contains-duplicate/" },
      { lc:true, num:"349", title:"Intersection of Two Arrays",        diff:"Easy",   url:"https://leetcode.com/problems/intersection-of-two-arrays/" },
      { lc:true, num:"383", title:"Ransom Note",                       diff:"Easy",   url:"https://leetcode.com/problems/ransom-note/" },
      { lc:true, num:"128", title:"Longest Consecutive Sequence",      diff:"Medium", url:"https://leetcode.com/problems/longest-consecutive-sequence/" },
      { lc:true, num:"454", title:"4Sum II",                           diff:"Medium", url:"https://leetcode.com/problems/4sum-ii/" },
      { lc:true, num:"347", title:"Top K Frequent Elements",           diff:"Medium", url:"https://leetcode.com/problems/top-k-frequent-elements/" },
    ]},
    { day: 9, title: "Math & Number Theory", badge: "badge-easy", badgeText: "Easy", topics: [
      "GCD and LCM using Euclidean algorithm",
      "Prime number check (trial division)",
      "Sieve of Eratosthenes",
      "Modular arithmetic basics",
      "Power function and fast exponentiation"
    ]},
    { day: 10, title: "Linked List Basics", badge: "badge-easy", badgeText: "Easy", topics: [
      "Singly linked list — node structure",
      "Insert at head, tail, and given position",
      "Delete a node by value",
      "Reverse a linked list",
      "Detect cycle using Floyd's algorithm"
    ]},
    { day: 11, title: "Linked List — Medium", badge: "badge-med", badgeText: "Medium", topics: [
      "Merge two sorted linked lists",
      "Find middle node using slow-fast pointer",
      "Remove nth node from end",
      "Check if linked list is a palindrome"
    ]},
    { day: 12, title: "Linked List Practice", badge: "badge-practice", badgeText: "Practice", isPractice: true, topics: [
      { lc:true, num:"206", title:"Reverse Linked List",                diff:"Easy",   url:"https://leetcode.com/problems/reverse-linked-list/" },
      { lc:true, num:"21",  title:"Merge Two Sorted Lists",             diff:"Easy",   url:"https://leetcode.com/problems/merge-two-sorted-lists/" },
      { lc:true, num:"141", title:"Linked List Cycle",                  diff:"Easy",   url:"https://leetcode.com/problems/linked-list-cycle/" },
      { lc:true, num:"876", title:"Middle of the Linked List",          diff:"Easy",   url:"https://leetcode.com/problems/middle-of-the-linked-list/" },
      { lc:true, num:"83",  title:"Remove Duplicates from Sorted List", diff:"Easy",   url:"https://leetcode.com/problems/remove-duplicates-from-sorted-list/" },
      { lc:true, num:"19",  title:"Remove Nth Node From End of List",   diff:"Medium", url:"https://leetcode.com/problems/remove-nth-node-from-end-of-list/" },
      { lc:true, num:"2",   title:"Add Two Numbers",                    diff:"Medium", url:"https://leetcode.com/problems/add-two-numbers/" },
      { lc:true, num:"142", title:"Linked List Cycle II",               diff:"Medium", url:"https://leetcode.com/problems/linked-list-cycle-ii/" },
      { lc:true, num:"234", title:"Palindrome Linked List",             diff:"Easy",   url:"https://leetcode.com/problems/palindrome-linked-list/" },
      { lc:true, num:"148", title:"Sort List",                          diff:"Medium", url:"https://leetcode.com/problems/sort-list/" },
      { lc:true, num:"160", title:"Intersection of Two Linked Lists",   diff:"Easy",   url:"https://leetcode.com/problems/intersection-of-two-linked-lists/" },
    ]},
    { day: 13, title: "Stack", badge: "badge-med", badgeText: "Medium", topics: [
      "Stack using array or list",
      "Push, pop, peek operations",
      "Valid parentheses problem",
      "Next greater element using stack",
      "Min stack — get minimum in O(1)"
    ]},
    { day: 14, title: "Queue & Deque", badge: "badge-med", badgeText: "Medium", topics: [
      "Queue using array",
      "Queue implemented using two stacks",
      "Circular queue concept",
      "Deque — double ended queue",
      "Sliding window maximum using deque"
    ]},
  ]},

  { week: 3, wkLabel: "Week 3 — Stack, Queue, Binary Search & Sorting", wkClass: "wk3", days: [
    { day: 15, title: "Stack & Queue Practice", badge: "badge-practice", badgeText: "Practice", isPractice: true, topics: [
      { lc:true, num:"20",  title:"Valid Parentheses",                  diff:"Easy",   url:"https://leetcode.com/problems/valid-parentheses/" },
      { lc:true, num:"155", title:"Min Stack",                          diff:"Medium", url:"https://leetcode.com/problems/min-stack/" },
      { lc:true, num:"232", title:"Implement Queue using Stacks",       diff:"Easy",   url:"https://leetcode.com/problems/implement-queue-using-stacks/" },
      { lc:true, num:"225", title:"Implement Stack using Queues",       diff:"Easy",   url:"https://leetcode.com/problems/implement-stack-using-queues/" },
      { lc:true, num:"496", title:"Next Greater Element I",             diff:"Easy",   url:"https://leetcode.com/problems/next-greater-element-i/" },
      { lc:true, num:"739", title:"Daily Temperatures",                 diff:"Medium", url:"https://leetcode.com/problems/daily-temperatures/" },
      { lc:true, num:"239", title:"Sliding Window Maximum",             diff:"Hard",   url:"https://leetcode.com/problems/sliding-window-maximum/" },
      { lc:true, num:"150", title:"Evaluate Reverse Polish Notation",   diff:"Medium", url:"https://leetcode.com/problems/evaluate-reverse-polish-notation/" },
      { lc:true, num:"84",  title:"Largest Rectangle in Histogram",     diff:"Hard",   url:"https://leetcode.com/problems/largest-rectangle-in-histogram/" },
    ]},
    { day: 16, title: "Binary Search", badge: "badge-med", badgeText: "Medium", topics: [
      "Classic binary search on sorted array",
      "Find first and last occurrence of element",
      "Search in rotated sorted array",
      "Binary search on answer concept",
      "Lower bound and upper bound concept"
    ]},
    { day: 17, title: "Binary Search Practice", badge: "badge-practice", badgeText: "Practice", isPractice: true, topics: [
      { lc:true, num:"704", title:"Binary Search",                      diff:"Easy",   url:"https://leetcode.com/problems/binary-search/" },
      { lc:true, num:"35",  title:"Search Insert Position",             diff:"Easy",   url:"https://leetcode.com/problems/search-insert-position/" },
      { lc:true, num:"278", title:"First Bad Version",                  diff:"Easy",   url:"https://leetcode.com/problems/first-bad-version/" },
      { lc:true, num:"374", title:"Guess Number Higher or Lower",       diff:"Easy",   url:"https://leetcode.com/problems/guess-number-higher-or-lower/" },
      { lc:true, num:"34",  title:"Find First and Last Position in Array", diff:"Medium", url:"https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/" },
      { lc:true, num:"33",  title:"Search in Rotated Sorted Array",     diff:"Medium", url:"https://leetcode.com/problems/search-in-rotated-sorted-array/" },
      { lc:true, num:"153", title:"Find Minimum in Rotated Sorted Array",diff:"Medium",url:"https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/" },
      { lc:true, num:"74",  title:"Search a 2D Matrix",                 diff:"Medium", url:"https://leetcode.com/problems/search-a-2d-matrix/" },
      { lc:true, num:"875", title:"Koko Eating Bananas",                diff:"Medium", url:"https://leetcode.com/problems/koko-eating-bananas/" },
      { lc:true, num:"162", title:"Find Peak Element",                  diff:"Medium", url:"https://leetcode.com/problems/find-peak-element/" },
    ]},
    { day: 18, title: "Sorting Algorithms", badge: "badge-easy", badgeText: "Easy", topics: [
      "Bubble sort — code and understand",
      "Selection sort — code and understand",
      "Insertion sort — code and understand",
      "Merge sort — understand and implement",
      "Quick sort — understand and implement",
      "Know time & space complexity of all 5"
    ]},
    { day: 19, title: "Week 3 Revision", badge: "badge-rev", badgeText: "Revision", topics: [
      "Re-solve 1 binary search problem",
      "Re-solve 1 stack or queue problem",
      "Re-solve 1 linked list problem",
      "Attempt 1 timed set on GFG (45 mins)"
    ]},
  ]},

  { week: 4, wkLabel: "Week 4 — Recursion, Trees & BST", wkClass: "wk1", days: [
    { day: 20, title: "Recursion & Backtracking", badge: "badge-easy", badgeText: "Easy", topics: [
      "Factorial using recursion",
      "Fibonacci using recursion",
      "Subset sum problem concept",
      "Generate all permutations of a string",
      "Understand the call stack with recursion diagrams"
    ]},
    { day: 21, title: "Binary Tree Basics", badge: "badge-easy", badgeText: "Easy", topics: [
      "Tree node structure in code",
      "Inorder traversal — Left Root Right",
      "Preorder traversal — Root Left Right",
      "Postorder traversal — Left Right Root",
      "Height and depth of a binary tree",
      "Level order traversal using BFS"
    ]},
    { day: 22, title: "BST", badge: "badge-med", badgeText: "Medium", topics: [
      "BST search operation",
      "BST insert operation",
      "BST delete operation",
      "Validate whether a tree is a valid BST",
      "Lowest common ancestor in BST"
    ]},
    { day: 23, title: "Trees Practice", badge: "badge-practice", badgeText: "Practice", isPractice: true, topics: [
      { lc:true, num:"104", title:"Maximum Depth of Binary Tree",       diff:"Easy",   url:"https://leetcode.com/problems/maximum-depth-of-binary-tree/" },
      { lc:true, num:"226", title:"Invert Binary Tree",                 diff:"Easy",   url:"https://leetcode.com/problems/invert-binary-tree/" },
      { lc:true, num:"572", title:"Subtree of Another Tree",            diff:"Easy",   url:"https://leetcode.com/problems/subtree-of-another-tree/" },
      { lc:true, num:"100", title:"Same Tree",                          diff:"Easy",   url:"https://leetcode.com/problems/same-tree/" },
      { lc:true, num:"543", title:"Diameter of Binary Tree",            diff:"Easy",   url:"https://leetcode.com/problems/diameter-of-binary-tree/" },
      { lc:true, num:"110", title:"Balanced Binary Tree",               diff:"Easy",   url:"https://leetcode.com/problems/balanced-binary-tree/" },
      { lc:true, num:"102", title:"Binary Tree Level Order Traversal",  diff:"Medium", url:"https://leetcode.com/problems/binary-tree-level-order-traversal/" },
      { lc:true, num:"112", title:"Path Sum",                           diff:"Easy",   url:"https://leetcode.com/problems/path-sum/" },
      { lc:true, num:"235", title:"LCA of a Binary Search Tree",        diff:"Medium", url:"https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/" },
      { lc:true, num:"98",  title:"Validate Binary Search Tree",        diff:"Medium", url:"https://leetcode.com/problems/validate-binary-search-tree/" },
      { lc:true, num:"230", title:"Kth Smallest Element in a BST",      diff:"Medium", url:"https://leetcode.com/problems/kth-smallest-element-in-a-bst/" },
      { lc:true, num:"124", title:"Binary Tree Maximum Path Sum",       diff:"Hard",   url:"https://leetcode.com/problems/binary-tree-maximum-path-sum/" },
    ]},
    { day: 24, title: "Graphs — BFS & DFS", badge: "badge-med", badgeText: "Medium", topics: [
      "Graph representation using adjacency list",
      "Graph representation using adjacency matrix",
      "BFS traversal using queue",
      "DFS traversal using recursion or stack",
      "Find all connected components in a graph"
    ]},
    { day: 25, title: "Graphs — Problems", badge: "badge-med", badgeText: "Medium", topics: [
      "Cycle detection in undirected graph",
      "Cycle detection in directed graph",
      "Topological sort using DFS method",
      "Number of islands problem"
    ]},
    { day: 26, title: "Graphs Practice", badge: "badge-practice", badgeText: "Practice", isPractice: true, topics: [
      { lc:true, num:"200", title:"Number of Islands",                  diff:"Medium", url:"https://leetcode.com/problems/number-of-islands/" },
      { lc:true, num:"133", title:"Clone Graph",                        diff:"Medium", url:"https://leetcode.com/problems/clone-graph/" },
      { lc:true, num:"695", title:"Max Area of Island",                 diff:"Medium", url:"https://leetcode.com/problems/max-area-of-island/" },
      { lc:true, num:"542", title:"01 Matrix",                          diff:"Medium", url:"https://leetcode.com/problems/01-matrix/" },
      { lc:true, num:"994", title:"Rotting Oranges",                    diff:"Medium", url:"https://leetcode.com/problems/rotting-oranges/" },
      { lc:true, num:"207", title:"Course Schedule",                    diff:"Medium", url:"https://leetcode.com/problems/course-schedule/" },
      { lc:true, num:"210", title:"Course Schedule II",                 diff:"Medium", url:"https://leetcode.com/problems/course-schedule-ii/" },
      { lc:true, num:"417", title:"Pacific Atlantic Water Flow",        diff:"Medium", url:"https://leetcode.com/problems/pacific-atlantic-water-flow/" },
      { lc:true, num:"684", title:"Redundant Connection",               diff:"Medium", url:"https://leetcode.com/problems/redundant-connection/" },
      { lc:true, num:"785", title:"Is Graph Bipartite?",                diff:"Medium", url:"https://leetcode.com/problems/is-graph-bipartite/" },
    ]},
  ]},

  { week: 5, wkLabel: "Week 5 — Dynamic Programming & Final Prep", wkClass: "wk4", days: [
    { day: 27, title: "Dynamic Programming Intro", badge: "badge-easy", badgeText: "Easy", topics: [
      "Understand memoization — top-down approach",
      "Understand tabulation — bottom-up approach",
      "Fibonacci solved using DP",
      "Climbing stairs problem",
      "Min cost climbing stairs"
    ]},
    { day: 28, title: "DP — 1D Problems", badge: "badge-med", badgeText: "Medium", topics: [
      "House robber problem",
      "Coin change — find minimum coins",
      "Coin change — count number of ways",
      "Decode ways problem"
    ]},
    { day: 29, title: "DP — 2D Problems", badge: "badge-med", badgeText: "Medium", topics: [
      "0/1 Knapsack problem",
      "Longest common subsequence — LCS",
      "Unique paths in a grid",
      "Edit distance concept and solution"
    ]},
    { day: 30, title: "DP Practice", badge: "badge-practice", badgeText: "Practice", isPractice: true, topics: [
      { lc:true, num:"70",  title:"Climbing Stairs",                    diff:"Easy",   url:"https://leetcode.com/problems/climbing-stairs/" },
      { lc:true, num:"746", title:"Min Cost Climbing Stairs",           diff:"Easy",   url:"https://leetcode.com/problems/min-cost-climbing-stairs/" },
      { lc:true, num:"198", title:"House Robber",                       diff:"Medium", url:"https://leetcode.com/problems/house-robber/" },
      { lc:true, num:"213", title:"House Robber II",                    diff:"Medium", url:"https://leetcode.com/problems/house-robber-ii/" },
      { lc:true, num:"322", title:"Coin Change",                        diff:"Medium", url:"https://leetcode.com/problems/coin-change/" },
      { lc:true, num:"91",  title:"Decode Ways",                        diff:"Medium", url:"https://leetcode.com/problems/decode-ways/" },
      { lc:true, num:"62",  title:"Unique Paths",                       diff:"Medium", url:"https://leetcode.com/problems/unique-paths/" },
      { lc:true, num:"1143",title:"Longest Common Subsequence",         diff:"Medium", url:"https://leetcode.com/problems/longest-common-subsequence/" },
      { lc:true, num:"416", title:"Partition Equal Subset Sum",         diff:"Medium", url:"https://leetcode.com/problems/partition-equal-subset-sum/" },
      { lc:true, num:"300", title:"Longest Increasing Subsequence",     diff:"Medium", url:"https://leetcode.com/problems/longest-increasing-subsequence/" },
      { lc:true, num:"72",  title:"Edit Distance",                      diff:"Medium", url:"https://leetcode.com/problems/edit-distance/" },
      { lc:true, num:"518", title:"Coin Change II",                     diff:"Medium", url:"https://leetcode.com/problems/coin-change-ii/" },
    ]},
    { day: 31, title: "Heaps & Priority Queue", badge: "badge-med", badgeText: "Medium", topics: [
      "Min heap and max heap concept",
      "heapq in Python or PriorityQueue in Java",
      "K largest elements problem",
      "K smallest elements problem",
      "Merge K sorted lists"
    ]},
    { day: 32, title: "Mixed Practice", badge: "badge-med", badgeText: "Mixed", topics: [
      "Solve 5 random problems timed — 10 min each",
      "Cover: 1 array, 1 string, 1 tree, 1 graph, 1 DP",
      "Focus on writing clean code fast",
      "Note any pattern you missed"
    ]},
    { day: 33, title: "TCS NQT Mock Test 1", badge: "badge-mock", badgeText: "Mock", topics: [
      "Study TCS NQT exam pattern — 3 sections",
      "Attempt full mock on PrepInsta.com",
      "Time yourself strictly",
      "Note weak areas after test"
    ]},
    { day: 34, title: "Fix Weak Areas", badge: "badge-rev", badgeText: "Revision", topics: [
      "Review all wrong answers from mock test",
      "Re-study top 2 weak topics",
      "Solve 3 targeted problems on weak topics",
      "Review time complexity cheat sheet"
    ]},
    { day: 35, title: "TCS NQT Mock Test 2", badge: "badge-mock", badgeText: "Mock", topics: [
      "Attempt second full TCS NQT mock test",
      "Focus on time management this time",
      "Try to improve score from test 1",
      "Review mistakes after the test"
    ]},
    { day: 36, title: "Final Revision", badge: "badge-rev", badgeText: "Revision", topics: [
      "Revise all topic notes in one pass",
      "Check all patterns: two pointer, sliding window, BFS/DFS, DP",
      "Review edge cases — empty array, single element, negatives",
      "Quick-fire: solve 5 easy problems in 30 mins"
    ]},
    { day: 37, title: "Rest & Confidence", badge: "badge-rev", badgeText: "Ready!", topics: [
      "Light review of your own notes only — no new problems",
      "Eat well, stay hydrated",
      "Sleep 8 hours tonight",
      "You are ready. Trust your work."
    ]},
  ]},
];

function loadData() {
  try { return JSON.parse(localStorage.getItem(STORAGE_KEY) || '{}'); } catch { return {}; }
}
function saveData(data) { localStorage.setItem(STORAGE_KEY, JSON.stringify(data)); }
function getKey(day, i) { return `d${day}_t${i}`; }

function showToast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg; t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 2500);
}

function exportProgress() {
  const data = loadData();
  const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = 'dsa_progress.json';
  a.click();
  showToast('Progress exported as dsa_progress.json');
}

function importProgress(event) {
  const file = event.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = (e) => {
    try {
      const data = JSON.parse(e.target.result);
      localStorage.setItem(STORAGE_KEY, JSON.stringify(data));
      buildPlan();
      showToast('Progress imported successfully!');
    } catch { showToast('Invalid JSON file.'); }
  };
  reader.readAsText(file);
  event.target.value = '';
}

function resetAll() {
  if (!confirm('Reset all progress? This cannot be undone.')) return;
  localStorage.removeItem(STORAGE_KEY);
  buildPlan();
  showToast('Progress reset.');
}

function getTotalDays() {
  let count = 0;
  plan.forEach(w => count += w.days.length);
  return count;
}

function updateStats() {
  const data = loadData();
  let total = 0, done = 0, doneDays = 0;
  plan.forEach(w => w.days.forEach(d => {
    const n = d.topics.length; total += n;
    let dc = 0;
    d.topics.forEach((_, i) => { if (data[getKey(d.day, i)]) { done++; dc++; } });
    if (dc === n) doneDays++;
  }));
  const totalDays = getTotalDays();
  const pct = total ? Math.round(done / total * 100) : 0;
  document.getElementById('s-done').textContent = done;
  document.getElementById('s-total').textContent = total;
  document.getElementById('s-days').textContent = doneDays;
  document.getElementById('s-left').textContent = totalDays - doneDays;
  document.getElementById('main-bar').style.width = pct + '%';
  document.getElementById('pct-label').textContent = pct + '%';
}

function buildTopicItem(day, topic, i, data) {
  const key = getKey(day.day, i);
  const checked = !!data[key];

  if (topic && topic.lc) {
    // LeetCode practice item
    const item = document.createElement('div');
    item.className = 'lc-item';
    const diffClass = topic.diff === 'Easy' ? 'diff-easy' : topic.diff === 'Medium' ? 'diff-med' : 'diff-med';
    const label = document.createElement('label');
    label.className = checked ? 'checked-label' : '';
    label.id = `lbl-${day.day}-${i}`;
    label.innerHTML = `
      <input type="checkbox" id="chk-${day.day}-${i}" ${checked ? 'checked' : ''}/>
      <span class="lc-num">#${topic.num}</span>
      <span>${topic.title}<span class="${diffClass}">${topic.diff}</span>
      <a href="${topic.url}" target="_blank" rel="noopener" class="lc-link" onclick="event.stopPropagation()">↗ LeetCode</a></span>
    `;
    item.appendChild(label);
    item.querySelector('input').addEventListener('change', function() {
      const d2 = loadData();
      if (this.checked) d2[key] = true; else delete d2[key];
      saveData(d2);
      label.className = this.checked ? 'checked-label' : '';
      refreshDayCard(day.day, day);
      updateStats();
    });
    return item;
  } else {
    // Regular topic item
    const item = document.createElement('div');
    item.className = 'topic-item';
    item.innerHTML = `
      <label class="${checked ? 'checked-label' : ''}" id="lbl-${day.day}-${i}">
        <input type="checkbox" id="chk-${day.day}-${i}" ${checked ? 'checked' : ''}/>
        ${topic}
      </label>`;
    item.querySelector('input').addEventListener('change', function() {
      const d2 = loadData();
      if (this.checked) d2[key] = true; else delete d2[key];
      saveData(d2);
      document.getElementById(`lbl-${day.day}-${i}`).className = this.checked ? 'checked-label' : '';
      refreshDayCard(day.day, day);
      updateStats();
    });
    return item;
  }
}

function buildPlan() {
  const data = loadData();
  const container = document.getElementById('plan');
  container.innerHTML = '';

  plan.forEach(week => {
    const wDiv = document.createElement('div');
    wDiv.className = 'week';
    const wTitle = document.createElement('div');
    wTitle.className = `week-title ${week.wkClass}`;
    wTitle.textContent = week.wkLabel;
    wDiv.appendChild(wTitle);

    week.days.forEach(day => {
      const totalT = day.topics.length;
      const doneT = day.topics.filter((_, i) => data[getKey(day.day, i)]).length;
      const allDone = doneT === totalT;

      const card = document.createElement('div');
      card.className = 'day-card' + (day.isPractice ? ' practice-card' : '') + (allDone ? ' done' : '');
      card.id = `card-day-${day.day}`;

      const header = document.createElement('div');
      header.className = 'day-header';
      header.innerHTML = `
        <span class="day-num">Day ${day.day}</span>
        <span class="day-name${allDone ? ' done-text' : ''}" id="dn-${day.day}">${day.title}</span>
        ${day.isPractice && !allDone ? `<span style="font-size:10px;font-family:'JetBrains Mono',monospace;color:var(--muted);margin-right:2px">${doneT}/${totalT}</span>` : ''}
        ${allDone
          ? `<span class="day-complete-pill">✓ Complete</span>`
          : `<span class="day-badge ${day.badge}">${day.badgeText}</span>`}
        <span class="chevron" id="chev-${day.day}">›</span>
      `;

      const topicsDiv = document.createElement('div');
      topicsDiv.className = 'topics';
      topicsDiv.id = `topics-${day.day}`;

      if (day.isPractice) {
        const lbl = document.createElement('div');
        lbl.className = 'practice-section-label';
        lbl.textContent = `LeetCode Problems — ${day.topics.length} questions`;
        topicsDiv.appendChild(lbl);
      }

      day.topics.forEach((topic, i) => {
        topicsDiv.appendChild(buildTopicItem(day, topic, i, data));
      });

      header.addEventListener('click', () => {
        document.getElementById(`topics-${day.day}`).classList.toggle('open');
        document.getElementById(`chev-${day.day}`).classList.toggle('open');
      });

      card.appendChild(header);
      card.appendChild(topicsDiv);
      wDiv.appendChild(card);
    });
    container.appendChild(wDiv);
  });
  updateStats();
}

function refreshDayCard(dayNum, day) {
  const data = loadData();
  const totalT = day.topics.length;
  const doneT = day.topics.filter((_, i) => data[getKey(dayNum, i)]).length;
  const allDone = doneT === totalT;
  const card = document.getElementById(`card-day-${dayNum}`);
  if (!card) return;
  card.className = 'day-card' + (day.isPractice ? ' practice-card' : '') + (allDone ? ' done' : '');
  const dn = document.getElementById(`dn-${dayNum}`);
  if (dn) dn.className = 'day-name' + (allDone ? ' done-text' : '');
}

buildPlan();
</script>
</body>
</html>

---

## 🐛 Errors

> Auto-scanned: 6/4/2026, 3:46:44 AM

✅ No errors found!
