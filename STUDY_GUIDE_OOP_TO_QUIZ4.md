# DATASCI 185 study guide: Python OOP through Practice Quiz 4 and JSON

This completed guide covers Lectures 14–20, the index supplement, Labs 5–8, Assignments 4–7, and Practice Quiz 4. Earlier Labs 5–6 and Assignment 4 are included as supporting prerequisites for the OOP and simulation material. Matching starter/solution notebooks share the same notes; they are listed in the coverage inventory below. Each notebook also contains its own **Study summary** Markdown cell at the bottom.

Every section explains concepts, function/method syntax, useful code blocks, how the exercises apply them, and quiz-relevant mistakes or extensions. Practice Quiz 4 has a full A–F walkthrough and 18 answered self-checks. Extensions are study possibilities, not predictions about the actual quiz.

## How to use this guide

Start with Lectures 14–16 for object state, mutation, and inheritance. Use Labs 7–8 to connect those ideas to model objects, chaining, and super. Work through Lecture 17 and Assignment 7 for the Pandas patterns tested directly in Quiz 4 A–C. Then attempt Quiz 4 D–F before reading the explanations. Finish Lectures 18–20 for the later merging, cleaning, recoding, grouping, and JSON material, which the practice test does not directly cover.

The code blocks are teaching examples, not one script to execute from top to bottom. Some are methods to insert inside an existing class; others rely on setup variables explained in that section. Dataset paths assume the corresponding notebook's folder. Existing course code and outputs are preserved; warnings about misleading original examples are explained in the notes.

## Contents

[Review of your saved Practice Quiz 4 attempt](#practice-quiz-4-personal-review)

1. [Lecture 14: classes, fractions, and operators](#study-section-1)
2. [Lecture 15: objects that remember and interact](#study-section-2)
3. [Lecture 16: composition, class variables, and inheritance](#study-section-3)
4. [Lecture 17: Pandas basics](#study-section-4)
5. [Lecture 17 supplement: loc, iloc, and indexes](#study-section-5)
6. [Lecture 18a: merging](#study-section-6)
7. [Lecture 18b: cleaning](#study-section-7)
8. [Lecture 18c: recoding](#study-section-8)
9. [Lecture 19: grouping and aggregation](#study-section-9)
10. [Lecture 20: JSON and nested data](#study-section-10)
11. [Lab 5: argument conventions (supporting prerequisite)](#study-section-11)
12. [Lab 6: simulation and the interactive game (supporting prerequisite)](#study-section-12)
13. [Lab 7: regression with classes](#study-section-13)
14. [Lab 8: method chaining and super](#study-section-14)
15. [Assignment 4: one-sided selection (supporting prerequisite)](#study-section-15)
16. [Assignment 5: zip, safe returns, lambda, and apply](#study-section-16)
17. [Assignment 6: mutual selection and experiments](#study-section-17)
18. [Assignment 7: complete Pandas workflows](#study-section-18)
19. [Practice Quiz 4: A–F and answered extensions](#study-section-19)

<a id="study-section-1"></a>

## 1. Lecture 14: classes, fractions, and operators

Source notebook: [Lecture14.ipynb](Lecture_14_Object_Oriented_Programming/Lecture14.ipynb)

**Purpose:** build a new kind of Python object, store its state, change it with methods, and define how ordinary operators work on it.

| New tool | How to use it | What it does |
|---|---|---|
| `class` | `class Fraction:` | Defines an object type |
| `__init__` | `Fraction(3, 5)` | Initializes the new instance's attributes |
| `self.attribute` | `self.num = num` | Stores information on this particular object |
| Instance method | `f.reduce()` | Acts on the instance automatically supplied as `self` |
| `%`, `//` | `m % n`, `num // common` | Remainder and integer quotient for fraction reduction |
| `__str__`, `__repr__` | `str(f)`, `repr(f)` | Return user-facing and programmer-facing representations |
| Arithmetic dunders | `f1 + f2`, `f1 * f2` | Implement arithmetic for your class |
| Comparison dunders | `f1 == f2`, `f1 > f2` | Implement meaningful comparisons |
| `is` | `f1 is f2` | Tests whether two names refer to the same object |
| `assert` | `assert den != 0, "denominator cannot be zero"` | Stops execution when a required condition is false |

#### Classes, instances, state, and methods

A **class** defines a kind of object. An **instance** is one particular object created from that class. An object's **state** is the information it currently stores. A **method** is a function attached to the class that can use or change that state.

```python
class Fraction:
    def __init__(self, num, den):
        self.num = num
        self.den = den

f = Fraction(3, 5)
```

`Fraction` is the class; `f` is an instance. `f.num` is 3 and `f.den` is 5. The parameters `num` and `den` are temporary names during initialization; `self.num` and `self.den` retain those values on the object.

**`self` means the instance receiving the method call.** In `f.reduce()`, Python supplies `f` as `self`. For ordinary instance methods, `f.reduce()` corresponds to `Fraction.reduce(f)`. You do not pass `self` explicitly in the first form.

`__init__` initializes a newly created instance. The course calls it the constructor; technically, object creation precedes its initialization. It should not return a fraction or `self`.

#### Attributes versus method calls

```python
f.num          # stored information: attribute
f.reduce()     # perform an action: method call
```

This is the same distinction as `array.shape` versus `array.mean()`, and `df.columns` versus `df.head()`. Parentheses call a callable. Square brackets select information, as in `df.iloc[0, 2]`.

#### Reducing a fraction

The greatest common divisor of 6 and 8 is 2. Dividing both by 2 produces 3/4. The notebook implements Euclid's algorithm:

```python
def gcd(m, n):
    while m % n != 0:
        m, n = n, m % n
    return n
```

`%` gives a remainder; `//` gives floor division, which produces the integer quotient here. `/` produces true division. Simultaneous assignment evaluates the right side using the old values before updating either variable. For `(6, 8)`, the successive pairs are `(8, 6)`, `(6, 2)`, then the remainder is zero and the answer is 2. This implementation assumes a positive second argument.

```python
def reduce(self):
    common = gcd(self.num, self.den)
    self.num //= common
    self.den //= common
```

This **mutates** the fraction. With no explicit return, its return value is `None`. Consequently, `f = f.reduce()` replaces the name `f` with `None`; use `f.reduce()` alone.

#### String representations

```python
def __str__(self):
    return f"{self.num}/{self.den}"

def __repr__(self):
    return f"Fraction({self.num}, {self.den})"
```

`print(f)` and `str(f)` use `__str__`. `repr(f)` and the representation of an object inside a list use `__repr__`. Both methods must **return strings**, rather than merely print them. Jupyter's `display` can use richer display methods, but for these simple custom classes the plain representation explains the notebook's output.

#### Operator overloading

An operator can mean something appropriate for your class because special methods implement its behavior.

| Expression | Relevant method | Fraction rule |
|---|---|---|
| `a + b` | `__add__` | `(ad + cb)/(bd)` |
| `a - b` | `__sub__` | `(ad - cb)/(bd)` |
| `a * b` | `__mul__` | `(ac)/(bd)` |
| `a / b` | `__truediv__` | `(ad)/(bc)` |
| `a == b` | `__eq__` | `ad == cb` |
| `a != b` | `__ne__` | unequal values |
| `a > b`, `a >= b` | `__gt__`, `__ge__` | compare cross-products |
| `a < b`, `a <= b` | `__lt__`, `__le__` | compare cross-products |

In the formulas, the two fractions are `a/b` and `c/d`. For positive denominators, compare the integer cross-products without introducing floating-point rounding.

```python
def __add__(self, other):
    result = Fraction(self.num * other.den + other.num * self.den,
                      self.den * other.den)
    result.reduce()
    return result
```

This returns a **new object**. The original operands remain unchanged. The subtraction exercise changes the plus between the cross-products to minus. Multiplication multiplies numerators and denominators; division multiplies by the reciprocal. Dividing by a fraction with numerator zero must fail because the resulting denominator would be zero.

#### Equality versus identity

```python
a = Fraction(1, 2)
b = Fraction(2, 4)
c = a
```

After implementing value-based `__eq__`, `a == b` is true, but `a is b` is false. `a is c` is true: they are two names for the same object. `is` tests **identity**; `==` uses the class's equality behavior. Calling this “identity versus value equality” is clearer than the notebook's “shallow/deep equality” wording.

Without a custom `__eq__`, two separately created instances of this simple class compare unequal. Lists already have value-based equality, so two separate lists can compare equal.

#### All Lecture 14 exercises

1. **Subtraction:** construct the result with the subtraction formula, reduce it, and return it. `3/4 - 1/4` becomes `1/2`.
2. **`to_float`:** return `self.num / self.den`. This is useful for numerical comparison with ordinary arithmetic.
3. **`to_percent`:** multiply the fraction's value by 100 and format it. `return f"{100 * self.num / self.den:.{ndigits}f}%"` displays exactly the requested number of decimal places.
4. **Counter trace:** `Counter()` starts at 0; `Counter(10)` starts at 10. After `a.bump(3)`, `b.bump(3)`, `a.bump(3)`, the values are 6 and 13. Omitting `self` from the method signature breaks the bound method call.
5. **Broken classes:** `name = name` does not create `self.name`, so Dog's method raises `AttributeError`; Cat's missing `self` gives `TypeError`; Bird's bare `name` raises `NameError` in a clean scope, or accidentally reads an unrelated global if one exists. Use `self.name`.
6. **Identify objects:** `x.size` is an ndarray attribute; `x.sum()` is an ndarray method; `df.shape` is a DataFrame attribute; `df["skill"].mean()` is a Series method; `name.lower()` is a string method.
7. **Other operators:** use the table above. For `1/4` and `3/4`, product is `3/16`, quotient is `1/3`, and the comparison results in the exercise's order are false, false, true, true, true.
8. **Normalize signs:** if `den < 0`, negate **both** numerator and denominator. This preserves the fraction's value and makes cross-product comparison safe.
9. **Reject zero denominators:** put `assert den != 0, "denominator cannot be zero"` before using it.

One nuance: Python can derive `!=` from `__eq__` when `__ne__` is not supplied. Therefore the exercise's statement that every listed expression necessarily fails is too broad. For the course exercise, writing the requested method is still useful practice.

#### Useful code block: validating and normalizing initialization

```python
def __init__(self, num, den):
    assert den != 0, "denominator cannot be zero"
    if den < 0:
        num, den = -num, -den
    self.num = num
    self.den = den
```

Validate before storing invalid state. Normalizing the sign lets the comparison methods assume positive denominators. This initializer belongs inside `Fraction`.

**Quiz connection:** Practice Quiz 4's `Runner.log_run` uses the same `self`, mutation, and assertion ideas. Be able to explain what a method changes separately from what it returns.

---

<a id="study-section-2"></a>

## 2. Lecture 15: objects that remember and interact

Source notebook: [Lecture15_OOP_with_mutual_selection.ipynb](Lecture_15_OOP_continued/Lecture15_OOP_with_mutual_selection.ipynb)

**Purpose:** model many independent decision makers, each with its own memory, and make their objects interact.

| Method or pattern | Usage | Effect / return value |
|---|---|---|
| Constructor default | `Student("Craig", 0.37)` | Uses the default strategy of zero |
| Getter | `person.get_lab_skills()` | Returns a value without changing the person |
| Validating setter | `person.set_strategy(2)` | Changes the strategy after checking it |
| `rate` | `a.rate(b)` | Returns how highly A rates B |
| `consider` | `a.consider(b)` | Updates A's meeting state and returns a Boolean |
| `pair_with` | `a.pair_with(b)` | Links both objects; returns `None` |
| None check | `person.get_partner() is None` | Detects an absent partner safely |
| `random.shuffle` | `random.shuffle(looking)` | Shuffles an existing list in place; returns `None` |
| Step-size range | `range(0, len(looking), 2)` | Visits the start of each pair |
| Roster/session/semester functions | `run_semester(build_roster(2), 12)` | Build fresh objects and simulate their changing state |

#### Why introduce Student objects?

The earlier partner simulation tracked one decision maker with local variables. Mutual selection needs 50 separate decision makers, each with persistent memory. An object keeps each student's information together.

| Attribute | Meaning |
|---|---|
| `name`, `lab_skills` | Who this person is and their skill |
| `strategy` | Number of meetings spent observing |
| `benchmark` | Best rating seen during observation |
| `num_met` | Number of meetings so far |
| `partner` | Another Student object, or `None` |

Values such as `benchmark = 0`, `num_met = 0`, and `partner = None` are initialized internally; they need not be constructor arguments.

#### Getters, setters, and validation

Getters return information; setters change it and can check constraints:

```python
def get_strategy(self):
    return self.strategy

def set_strategy(self, N):
    assert N >= 0, "a strategy cannot be negative"
    self.strategy = N
```

A failed assertion raises `AssertionError` before the assignment. **A setter does not protect direct assignments automatically.** `student.strategy = -3` still bypasses it, and `__init__` must also validate or call the setter. Assignment 6 explicitly addresses this gap. In ordinary Python these attributes remain publicly accessible.

#### `consider`: a method that both mutates and returns

```python
def rate(self, other):
    return other.get_lab_skills()

def consider(self, other):
    self.num_met += 1
    if self.num_met <= self.strategy:
        if self.rate(other) > self.benchmark:
            self.benchmark = self.rate(other)
        return False
    return self.rate(other) > self.benchmark
```

First count the meeting. During the first N meetings, update the benchmark and always reject. Starting with meeting N+1, accept only ratings **strictly greater** than the benchmark. The deciding phase does not keep updating the benchmark.

With N=2 and encountered skills 0.37, 9.76, 5.07, the responses are false, false, false. The benchmarks are 0.37, 9.76, 9.76; counts are 1, 2, 3. The third rejection is for insufficient quality, rather than ongoing observation.

`rate` is separate so a subclass can change what “quality” means without rewriting the meeting algorithm.

#### Linking two objects

```python
def pair_with(self, other):
    self.partner = other
    other.partner = self
```

The attributes store **references to the actual objects**, not their names and not copies. After `a.pair_with(b)`, `a.get_partner() is b` and `b.get_partner() is a` are both true. You can call `a.get_partner().get_lab_skills()` because the first method returns a Student.

If there is no partner, that chain fails: `None` has no Student methods. Check `partner is not None` before following it.

#### A session and a semester

`build_roster(N)` loops through the nested dictionary and creates one Student per name. `run_session` gathers unpaired students, shuffles that list, and visits pairs with `range(0, len(looking), 2)`. Index `i+1` gives the second person in each pair.

The simulation starts with an even number and removes people in pairs, so the searching list stays even. That is an **invariant**: a fact that should remain true throughout the algorithm. An arbitrary odd-size roster violates the assumption.

Both people must consider the meeting:

```python
a_yes = a.consider(b)
b_yes = b.consider(a)
if a_yes and b_yes:
    a.pair_with(b)
```

Do not combine these into `if a.consider(b) and b.consider(a)`. `and` short-circuits: when the first response is false, Python skips the second call, so the second student never records that meeting.

The last session pairs everyone remaining regardless of their preferences. This deadline belongs to the session algorithm, rather than one student's preference method. A semester calls the session method repeatedly, marking its final call as the deadline.

#### Mutation and aliases

`todd = roster[12]` does not copy Todd. Both expressions refer to one Student. Running a semester on the roster changes the object visible through `todd` too.

Running another semester on the same completed roster does nothing: everyone already has a partner. Independent trials require fresh objects or a complete reset. A shallow list copy, `roster.copy()`, still contains the same Student objects and does not solve this.

#### All Lecture 15 exercises

1. **First meetings:** A/B both use N=0, so both say true. C says true and D, using N=2, says false; D's benchmark becomes 6. E/F both use N=2 and both say false. Calling `consider` alone never actually links partners; the session must call `pair_with` after checking their answers.
2. **`report`:** read each attribute, check whether the partner is `None`, and print either the partner's name or “nobody.” This combines safe access, getters, and f-strings.
3. **Aliasing:** `c = a` creates no Student. There are two objects, not three. After `c.pair_with(b)`, both `a` and `c` report B as partner, and B reports A.
4. **One-sided pairing:** assigning only `x.partner = y` leaves Y unpaired. Y can be incorrectly selected again, and the searching list can become odd, breaking `looking[i+1]`.

#### Useful code block: safe access to a linked object

```python
partner = student.get_partner()
if partner is None:
    print("No partner yet")
else:
    print(partner.get_name(), partner.get_lab_skills())
```

This pattern also applies to pace buddies, missing search results, and model attributes that have not been initialized yet.

**Quiz connection:** the Runner setter that links two pace buddies is the same bidirectional-reference pattern as `pair_with`. A method can mutate two objects even when it returns nothing.

---

<a id="study-section-3"></a>

## 3. Lecture 16: composition, class variables, and inheritance

Source notebook: [Lecture16_OOP_Class_Variables_and_Inheritance.ipynb](Lecture_16_OOP__Class_Variables_and_Inheritance/Lecture16_OOP_Class_Variables_and_Inheritance.ipynb)

**Purpose:** organize a simulation into a container class, distinguish shared and individual state, and reuse existing behavior through subclasses.

| Tool | Usage | Why it is useful |
|---|---|---|
| Composition | `section.students` contains Student objects | Groups related objects under one controller |
| Object lookup | `section.get_student("Todd")` | Returns the actual object, or `None` |
| Class variable | `LabSection.num_sessions = 20` | Changes a shared setting |
| Keyword-only option | `student.set_strategy(6.5, floor=True)` | Explicitly chooses the fixed-floor rule |
| Inheritance | `class StudyGroupStudent(Student):` | Reuses Student's existing methods |
| Method override | Define a new `rate(self, other)` | Changes one part of inherited behavior |
| `isinstance` | `isinstance(abed, Student)` | Recognizes instances of the class and its subclasses |
| `@classmethod` | `StudyGroupStudent.set_bonus(7)` | Operates on a class supplied as `cls` |
| Alternative constructor | `StudyGroupStudent.from_student(s)` | Builds a new child instance from another object |
| Replacing a list member | `self.students[i] = new_student` | Actually updates the container |

#### Composition: a section contains students

`LabSection` stores `self.students`, creates those students in its initializer, and runs sessions using its own methods. This is **composition**: a LabSection *has* Students. `get_student(name)` searches the list and returns the matching object or `None`.

Each new section creates fresh students. Two sections built from the same input dictionary therefore have independent Student state. `section_a.get_student("Todd")` and `section_b.get_student("Todd")` identify different objects.

This is the same structure as Practice Quiz 4's RunClub containing Runner objects.

#### Class variables versus instance attributes

```python
class LabSection:
    num_sessions = 12
```

`num_sessions` lives on the class. `self.students` lives on each section instance. Changing `LabSection.num_sessions = 20` affects existing instances that obtain that value through class lookup.

But assigning `section_a.num_sessions = 7` creates an instance attribute that **shadows** the class attribute. It does not change the other sections. Also read the method itself: this notebook's `run_semester` uses `LabSection.num_sessions` explicitly, so an instance shadow does not change its loop. A method using `self.num_sessions` would see that instance shadow.

Keep mutable per-instance lists such as `students` inside `__init__`. A class-level `students = []` would accidentally share one list across sections.

#### A fixed-floor strategy

The revised setter has `set_strategy(self, N, *, floor=False)`. The bare `*` makes `floor` keyword-only. With `floor=True`, the method sets `strategy = 0` and `benchmark = N`. The student decides immediately against that fixed threshold instead of spending N meetings learning a benchmark.

```python
todd.set_strategy(6.5, floor=True)
```

N=6.5 here means a skill threshold, not six-and-a-half observation meetings. The floor is not lowered just because a meeting fails; the final-session rule still guarantees pairing under the lecture's assumptions.

The notebook tests different floors for different skill levels using fresh random classes and repeated semesters. The proposed floor of about 75% of one's own skill is an empirical rule to investigate, not a universal optimum. A best response to one population may cease to be best when everyone changes strategies.

#### Inheritance and overriding

```python
class StudyGroupStudent(Student):
    bonus = 5

    def rate(self, other):
        if isinstance(other, StudyGroupStudent):
            return other.get_lab_skills() + self.bonus
        return other.get_lab_skills()
```

Inheritance means a StudyGroupStudent *is a* Student and inherits its methods. Overriding `rate` changes an inherited `consider` method's behavior because it calls **`self.rate(other)`**. Python looks up that method on the actual receiving object.

The bonus changes the person's **rating of a candidate**, not the candidate's stored skills. If Abed and Troy are study-group members, Abed rates Troy's 5.66 skills as 10.66 with bonus 5. An ordinary Student still rates Troy as 5.66.

`type(abed) == Student` is false for a StudyGroupStudent; `isinstance(abed, Student)` is true. Use `isinstance` when subclasses should count.

#### Class methods

```python
@classmethod
def set_bonus(cls, value):
    assert value >= 0, "the bonus cannot be negative"
    cls.bonus = value
```

`self` receives an instance in an instance method; `cls` receives the class in a class method. Call `StudyGroupStudent.set_bonus(7)` to change the shared bonus. Existing objects see the change if they have not shadowed it.

Class methods can also be alternative constructors:

```python
@classmethod
def from_student(cls, student):
    return cls(student.get_name(), student.get_lab_skills(),
               student.get_strategy())
```

This creates a **new** object of `cls`. It does not transform the original object in place. This particular method copies only name, skills, and strategy; benchmark, meeting count, and partner are initialized afresh.

#### All Lecture 16 exercises

1. **`ExperimentSection.average_meetings`:** inherit LabSection, collect `get_num_met()` from its students, and average them. This summarizes how quickly partnerships form. The notebook's earlier class-variable change makes its two-person test print 1 and 20.
2. **`use_floor_rule(proportion)`:** for every student, call `set_strategy(proportion * student.get_lab_skills(), floor=True)`. With skills 5 and 6 and proportion .75, benchmarks become 3.75 and 4.5; strategies become zero.
3. **`test_with_floor_75`:** first give everyone the 75% rule, then override one target's floor and simulate. Order matters: applying the common rule afterward would erase the target's special rule.
4. **`from_student`:** create a StudyGroupStudent using the original student's getters and `cls(...)`.
5. **`add_study_group`:** replace matching entries with newly created StudyGroupStudents. Use `self.students[i] = ...`; assigning only `s = ...` to a loop variable does not replace the list entry. Convert before running the semester, because replacement resets state and existing references would still point at the old object.
6. **Challenge: study group and Todd:** repeat the floor experiment with and without the group, holding the comparison setup consistent.
7. **Challenge: population rule:** test floors `a + b * skill` and check whether individuals can improve by deviating. That is a search for a stable strategy rule. Monte Carlo noise means small apparent improvements need more trials before drawing conclusions.

#### Extension from Lab 8: call the parent's implementation with `super()`

```python
class SpecialStudent(Student):
    def __init__(self, name, lab_skills, strategy=0):
        super().__init__(name, lab_skills, strategy)
        self.notes = []
```

Use this when a child needs the parent's initialization plus extra state. If you define a new child initializer, the parent's initializer does not run automatically. Keep `notes` on each instance, rather than sharing a class-level list.

**Quiz connection:** `TrailRunner` is a subclass like `StudyGroupStudent`; its multiplier is like the shared bonus. `RunClub` contains Runner objects just as LabSection contains Student objects.

---

<a id="study-section-4"></a>

## 4. Lecture 17: Pandas basics

Source notebook: [lecture_17_Pandas_basics.ipynb](Lecture_17_Pandas/lecture_17_Pandas_basics.ipynb)

**Purpose:** read tabular data, inspect its structure, select and sort observations, construct new variables, and compare subsets graphically. A DataFrame is a two-dimensional labeled table; one selected column is usually a one-dimensional Series.

#### New functions, methods, and attributes

| Tool | Example | Result / use |
|---|---|---|
| `pd.read_csv` | `df = pd.read_csv("data/features.csv")` | Loads a CSV into a DataFrame |
| `type` | `type(df["mpg"])` | Checks whether you have a DataFrame, Series, scalar, etc. |
| `.head` | `df.head(10)` | First ten rows, for inspection |
| `.columns` | `names = df.columns` | An Index of column labels; no parentheses |
| `.values` | `df.columns.values` | Underlying array of column labels in this example |
| `.shape` | `nrows, ncols = df.shape` | Tuple giving table dimensions |
| `len` | `len(df)` | Number of rows, not number of cells |
| `.index` | `df.index` | Row labels, separate from ordinary columns |
| `.sort_values` | `df.sort_values("mpg", ascending=False)` | Returns rows ordered by a column |
| `.iloc` | `df.iloc[20:30, 0:3]` | Selects by zero-based row/column positions |
| `.loc` | `df.loc[10, "vehicle_id"]` | Selects by row and column labels |
| `.set_index` / `.reset_index` | `df.set_index("vehicle_id")` | Moves between a data column and row labels |
| Series `.apply` | `df["height"].apply(convert)` | Calls a function on each element; returns a Series |
| Column assignment | `df["BMI"] = weight_kg / height_m**2` | Creates or replaces a column |
| `np.random.choice` | `np.random.choice(["A", "B"], size=len(df))` | One randomly chosen group label per row |
| `.query` | `df.query("mpg >= @threshold")` | Returns rows satisfying a logical expression |
| `pd.unique` / `.unique` | `df["cylinders"].unique()` | Distinct category values |
| `.nunique` | `df["vehicle_id"].nunique()` | Number of distinct nonmissing values by default |
| `.mean` | `df["Age"].mean()` | Mean of a numeric Series |

Paths are relative to the working directory. `../` means the parent directory; a path is not automatically resolved relative to whichever notebook happens to be visible.

#### Selecting columns: know the result's type

```python
mpg = df["mpg"]                         # Series
mpg_table = df[["mpg"]]                  # one-column DataFrame
subset = df[["weight", "mpg"]]          # two-column DataFrame
names = df.columns
same_subset = df[[names[4], names[0]]]   # use column-label positions
```

The outer brackets perform selection; the inner brackets build a list of column labels. The leftmost row index shown by Pandas is not an extra data column.

#### Positional and label selection

```python
df.iloc[28:33, 4:7]      # positions 28–32, columns 4–6: 5 rows × 3 columns
df.iloc[:, 2:5]          # every row; columns 2, 3, 4
df.iloc[[0, 4, 396], :]  # three specific row positions
df.iloc[-5:, :]         # last five rows
df.loc[10, "mpg"]       # row LABEL 10, column label mpg
```

`.iloc` excludes its stopping position. `.loc` label slices normally include the ending label when it is present and the slice is valid for the index. Both use square brackets, not method-call parentheses. See the supplementary notebook for changing indexes.

#### Useful code block: the best five entire observations

```python
top_five = df.sort_values("mpg", ascending=False).iloc[:5, :]
```

Sort the table, then select rows. Sorting only `df["mpg"]` loses the other car information. Sorting retains original row labels, so use `.iloc` for the first five positions. `ascending=True` is lowest first; `False` is highest first. With several sort columns, the first controls the main ordering and subsequent columns break ties.

#### Useful code block: transformations with `.apply` and arithmetic

```python
def inches_to_meters(inches):
    return inches * 0.0254

height_m = df_ht_wt["Height(Inches)"].apply(inches_to_meters)
weight_kg = df_ht_wt["Weight(Pounds)"] * 0.45359237
df_ht_wt["BMI"] = weight_kg / height_m**2
```

Pass the function itself to `.apply`, without calling it first: `.apply(inches_to_meters)`. Ordinary arithmetic works element by element, so a loop is unnecessary for multiplication or subtraction. Series arithmetic also aligns values by **index labels**, which matters if Series came from different subsets or sorts. Keep units consistent before calculating formulas. Giving a Series `.name` changes its label; it does not by itself add that Series to the DataFrame.

#### Useful code block: queries, external variables, and unusual names

```python
threshold = 25
efficient = df.query("mpg >= @threshold")
both = df.query("cylinders == 8 and mpg >= @threshold")
tall = fifa.query("`Height(in cm)` > @avg_height")
blitz_losses = carlsen.query("format == 'Blitz' and result == 'Loss'")
```

Inside a query string, a bare name refers to a column; `@name` refers to a Python variable in the surrounding scope. Backticks surround column names containing spaces or punctuation. String values still need quotes. Use `==` to compare; `=` assigns. The variables need not literally be global: a suitable local variable can also be referenced with `@`.

#### Useful code block: random assignment and plotting categories

```python
df["GroupID"] = np.random.choice(["A", "B"], size=len(df))
for group in ["A", "B"]:
    part = df.query("GroupID == @group")
    print(group, len(part))
```

The output array must have one entry per row. Equal assignment probabilities do not guarantee exactly equal group counts.

```python
categories = [4, 6, 8]
for category in categories:
    part = df.query("cylinders == @category")
    plt.hist(part["mpg"], bins=range(0, 55, 5),
             alpha=0.5, label=str(category))
plt.xlabel("Miles per gallon")
plt.ylabel("Number of cars")
plt.legend(title="Cylinders")
plt.show()
```

Only the filtering and drawing repeat. Labels, legend, and `show()` belong after the loop for an overlapping figure. `alpha` controls transparency. Shared histogram bin edges make comparisons easier. A scatter plot compares two numeric measurements; a histogram shows the distribution of one numeric measurement.

#### What the exercises apply

- **Coffee:** read and inspect rows, select and count columns, sort dwell time, get a brand with `.loc`, convert IDR to USD by dividing by 16,500, filter by density/reviews, and plot sales against discounts by order method.
- **Chess:** select opponent names, sort opponent rating, take the last five games, calculate player-minus-opponent rating, filter Blitz losses, and compare rating distributions for Blitz and Rapid.
- **Netflix:** select title/type/year, sort by year and duration, take rows 100–109 and the first three columns, calculate years since release, filter 2015–2020 using `@` variables, and compare release-year histograms by type. Inspect the duration dtype before interpreting its sort: text such as `"90 min"` is not numeric duration, and seasons and minutes have different units.
- **Social media:** select columns by their labels' positions, sort usage and GPA, choose the top ten, randomly assign A/B, filter short sleep or high usage, and plot usage versus sleep by platform.
- **Cars and BMI:** practice precise selection, ranking, unit conversion, derived measurements, and categorical plots.

**Pitfalls:** `.iloc[0:9]` returns nine rows, so the top-ten exercise needs `:10` (as the solutions notebook shows). `sort_values` returns a result unless you explicitly use `inplace=True`; do not discard it accidentally. Never write `df = df.sort_values(..., inplace=True)`, because that assigns `None`. An in-place operation is not a general guarantee of zero copying or minimal memory use.

**Quiz connection:** Practice Quiz 4 A–C directly reuse this lecture: create a random column and query subsets, slice rows/columns and sort, then use `.apply` plus arithmetic to compute kinetic energy. Changing names and unit-conversion constants does not change the underlying workflow.

---

<a id="study-section-5"></a>

## 5. Lecture 17 supplement: loc, iloc, and indexes

Source notebook: [lecture_17_loc_and_iloc.ipynb](Lecture_17_Pandas/lecture_17_loc_and_iloc.ipynb)

**Purpose:** distinguish an observation's current position from its row label. They happen to coincide in a fresh default-index CSV, but they do not mean the same thing.

| Tool | Meaning | Example |
|---|---|---|
| `.columns` | Labels of ordinary data columns | `df.columns` |
| `.index` | Labels identifying rows | `df.index` |
| `.iloc[row, col]` | Select by integer position | `df.iloc[0, 0]` |
| `.loc[row_label, col_label]` | Select by labels | `df.loc["C-1689780", "mpg"]` |
| `.set_index` | Make a column the row index | `by_id = df.set_index("vehicle_id")` |
| `.reset_index` | Move the index back into a column | `regular = by_id.reset_index()` |
| `.nunique` | Count distinct nonmissing entries | `df["vehicle_id"].nunique()` |
| `pd.RangeIndex` | Construct integer row labels | `pd.RangeIndex(30, 30 + len(df))` |

#### Useful code block: retrieve a car by its identifier

```python
assert df["vehicle_id"].nunique() == len(df)
by_id = df.set_index("vehicle_id")
value_by_position = by_id.iloc[0, 0]
value_by_label = by_id.loc["C-1689780", "mpg"]
```

In this notebook those two lookups retrieve the same first car's mpg. The assertion checks the supplied dataset's identifiers for uniqueness. A general identifier check would also check that none are missing.

By default, `set_index` removes `vehicle_id` from the ordinary columns. It remains available as the index. The original `df` remains unchanged when you assign the result to `by_id`.

#### Useful code block: demonstrate that labels are not positions

```python
shifted = df.copy()
shifted.index = pd.RangeIndex(30, 30 + len(shifted))
first_row = shifted.iloc[0]           # first row by position
same_row = shifted.loc[30]           # first row has label 30
# shifted.loc[0] raises KeyError: there is no row label 0
```

An assigned index must have exactly as many labels as there are rows. Changing labels does not change the data's row order.

#### Slices, dimensions, and resetting

`.iloc[1:6]` selects positions 1–5; stop 6 is excluded. `.loc[1:6]` is a label slice and ordinarily includes label 6 when present in a valid slice. With an identifier index, use identifier labels in `.loc`. After sorting, retained labels identify old rows while `.iloc` follows the new order.

`df.iloc[0]` usually returns a Series for one row; `df.iloc[[0]]` returns a one-row DataFrame. Selecting both a single row and a single column yields a scalar for a unique match. Duplicate row labels can make a `.loc` lookup return several observations.

```python
by_id = by_id.reset_index()                 # keep old IDs as a column
fresh_labels = filtered.reset_index(drop=True)  # discard the old index
```

`inplace=True` changes the receiving DataFrame and returns `None`. Otherwise, keep the returned result. Do not assume `inplace=True` means an operation allocates no temporary memory.

**Quiz extension:** if asked for the five highest-scoring rows, use `sort_values(...).iloc[:5]`, not `.loc[:4]`. The latter refers to labels, which sorting does not necessarily renumber.

---

<a id="study-section-6"></a>

## 6. Lecture 18a: merging

Source notebook: [lecture_18a_merging.ipynb](lecture_18_Merging_Cleaning_Recoding/lecture_18a_merging.ipynb)

**Purpose:** attach information from another table by matching identifiers. First identify what one row represents in each table; this determines which keys make a valid match.

#### New tools

| Tool | Example | Meaning |
|---|---|---|
| `pd.merge` | `pd.merge(results, races, on="raceId", how="left")` | Match rows on a key and combine columns |
| `left`, `right` | `left=results, right=races` | Identify the two input tables |
| `how="left"` | Keep results as the primary table | Preserve every left row's information, repeating rows if it has multiple matches |
| `how="right"` | Keep races as the primary table | Preserve every right row's information |
| `on` | `on="raceId"` | Key shared by the input tables |
| Composite key | `on=["state", "county"]` | Require equality in every listed key column |
| `suffixes` | `suffixes=("_county", "_state")` | Disambiguate non-key columns with the same name |
| `.item()` | `race_info["year"].item()` | Extract a scalar only when the Series has exactly one element |

An entity relationship diagram shows which identifiers connect tables. In the F1 example, results identify races using `raceId`; races identify circuits using `circuitId`. Several results belong to one race, and several races can use one circuit.

#### Useful code block: combine results, races, and circuits

```python
race_details = races[["raceId", "year", "circuitId", "name"]]
joined = pd.merge(results, race_details, on="raceId", how="left")
joined = pd.merge(
    joined,
    circuits[["circuitId", "name", "location", "country"]],
    on="circuitId", how="left", suffixes=("", "_circuit")
)
```

Selecting columns before merging keeps only the information you need, but remember to retain the matching key. The suffix keeps the race name separate from the circuit name. An unmatched right-side value becomes missing; it does not erase a row retained by a left merge.

Merging matches **key values**, not current row positions. Stacking rows with concatenation is a different task: it adds observations instead of looking up matching attributes. Unequal row counts do not themselves forbid concatenation; the important question is whether stacking is the operation you intend.

#### Useful code block: matching counties correctly

```python
county_votes = pres_county[["state", "county", "current_votes"]]
candidate_votes = pd.merge(
    pres_candidates, county_votes,
    on=["state", "county"], how="left"
)
candidate_votes["vote_pct"] = (
    100 * candidate_votes["total_votes"] / candidate_votes["current_votes"]
)
```

`Washington County` occurs in several states. Matching only county names would attach unrelated states' totals and multiply rows. State plus county supplies the appropriate identity in this dataset.

#### Why a merge can add rows

A left merge does not promise the same number of output rows as the left table. If one left row's key matches two right rows, it appears twice. If a key occurs m times on the left and n times on the right, that key contributes m×n matched combinations.

Check `len`, `.shape`, and key uniqueness when the result is unexpectedly large. A state-level merge with Senate results can produce multiple rows for Georgia because the right table has multiple elections associated with that state. Changing the join type does not fix an insufficient key.

#### What all the exercises apply

1. **Navigate House tables:** take a candidate's district, query the district table, and calculate candidate votes divided by district votes. `.item()` is appropriate only after verifying one district row matched.
2. **Merge House tables:** join `district` and `current_votes` onto every candidate. Vote percentages within a district should sum to approximately 100 when the candidate totals cover the counted votes.
3. **County versus state totals:** merged totals get suffixes. Compute county total / state total, not the reverse. The aggregate `United States` row on the right does not appear unless some retained left row matches it.
4. **Left versus right:** keeping all presidential states includes states without governor races, whose governor values are missing. Keeping governor rows retains the 11 states with those races.
5. **Unexpected extra rows:** inspect Georgia's repeated Senate key; a one-to-many join repeats the state row.
6. **Composite county key:** compare the incorrect county-only join with the correct state/county join, then calculate candidate shares for Fulton County, Georgia.

#### Useful quiz extensions

`how="inner"` keeps matched keys; `how="outer"` also includes unmatched rows from either side. If keys use different names, `left_on="driverId", right_on="driverRef"` makes the correspondence explicit. For an expected many-results-to-one-race join, `validate="many_to_one"` can check that the right keys are unique.

Ask before every merge: **what is one row, what identifies it, which side must be kept, and how many matches should each row have?**

---

<a id="study-section-7"></a>

## 7. Lecture 18b: cleaning

Source notebook: [lecture_18b_cleaning.ipynb](lecture_18_Merging_Cleaning_Recoding/lecture_18b_cleaning.ipynb)

**Purpose:** make recorded values accurately represent the underlying information before calculating statistics. Looking numeric and being stored as numbers are different things.

#### New tools

| Tool | Example | Use |
|---|---|---|
| `.dtypes` | `df.dtypes` | Inspect the stored type of each column |
| `.rename` | `df.rename(columns={"votes": "total_votes"})` | Change column labels |
| String accessor `.str` | `df["alt"].str.isnumeric()` | Apply string operations to a Series |
| `.str.isnumeric()` | `s.str.isnumeric()` | Character-based numeric check, not a complete number parser |
| `pd.unique` | `pd.unique(s)` | Reveal exact recorded strings and unexpected codes |
| `.replace` | `s.replace({"twenty": 20, "": np.nan})` | Replace specified recorded values |
| `np.nan` | Store missing numeric information | Missing is different from zero |
| `pd.to_numeric` | `pd.to_numeric(s)` | Parse numeric strings into numeric values |
| `errors="coerce"` | `pd.to_numeric(s, errors="coerce")` | Convert unparseable entries to missing |
| `.isna()` | `s.isna()` | Identify missing entries |
| `.to_csv` | `df.to_csv("clean.csv", index=False)` | Save cleaned data without an extra index column |

#### Diagnose before converting

The circuits data's altitude column contains numbers stored as text, a negative value, and a missing-value marker. A displayed `"125"` can still be text. Checking `.dtypes` explains why a numeric mean may fail.

`"-7".isnumeric()` is false because the minus sign is not a numeric character. Decimal strings such as `"3.5"` also fail this test even though they are valid numbers. Missing and mixed-type entries complicate a string mask further. Use this method to investigate strings, not as a universal validity test for numbers.

#### Useful code block: inspect and convert known markers

```python
print(circuits.dtypes)
print(pd.unique(circuits["alt"]))
alt_clean = circuits["alt"].replace({r"\N": np.nan})
circuits["alt_numeric"] = pd.to_numeric(alt_clean)
print(circuits["alt_numeric"].mean())
```

`pd.to_numeric` parses valid negative strings itself. With its default error behavior, an unexpected bad entry stops conversion instead of being silently lost. A numeric Series can calculate means while skipping missing values by default; that changes the denominator to the number of observed values.

For the particular altitude data, `pd.to_numeric(circuits["alt"], errors="coerce")` can be a convenient shortcut after checking what will become missing. It is not an all-purpose cleaning strategy.

#### Useful code block: preserve recoverable values

```python
ages = df_survey["age"].replace({
    "twenty-one": "21", "twenty": "20",
    "twenty five": "25", "nineteen": "19", "": np.nan
})
df_survey["age_numeric"] = pd.to_numeric(ages)

votes = df_typed["votes_typed"].str.replace(",", "", regex=False)
votes = votes.replace("N/A", np.nan)
df_typed["votes_clean"] = pd.to_numeric(votes)
```

Coercing `"twenty-one"` or `"8,390"` directly would discard information you can recover. If the discarded values differ systematically from the others, the resulting mean can be misleading. Keep a new cleaned column when that helps you compare with the raw data.

#### Renaming, exact strings, and saving

```python
gov = gov.rename(columns={"votes": "total_votes"})
circuits["country"] = circuits["country"].replace("USA", "United States")
circuits.to_csv("f1_clean_data/circuits_final.csv", index=False)
check = pd.read_csv("f1_clean_data/circuits_final.csv")
print(check.shape, check.columns, check.dtypes)
```

`.rename` changes labels; `.replace` changes values. Keep the returned result unless using an in-place operation. Saving with the default index can create an unwanted `Unnamed: 0` column on reload. Saving to a separate cleaned file preserves access to the raw records.

#### What all the exercises apply

- **Re-reading the cleaned file:** verify column names, row count, and numeric types; do not assume an export perfectly preserved your intended layout.
- **Country replacement:** standardize equivalent labels such as USA/United States.
- **Survey ages:** map written-out numbers and blanks deliberately before conversion.
- **Election aggregate row:** exclude `United States` when calculating the mean over individual states and DC; otherwise the aggregate is counted as an extra observation.
- **Consistent headers:** rename governor votes before comparing tables.
- **Hidden characters:** inspect unique candidate strings; a value containing extra spaces does not equal the clean string. Replace the actual recorded forms. `.str.strip()` is a useful extension when surrounding whitespace is accidental.
- **Party codes:** inspect which candidate a code denotes. Do not assume a familiar-looking code has the expected meaning in this dataset.
- **Typed votes:** remove thousands separators and preserve genuinely missing votes, then merge with county records on both state and county to check values.
- **Clean export:** save without the index and read it back; the requested state table should have 51 rows and two columns.

**Pitfalls in the notebook:** one example creates `Uniform_Resource_Locator` but tries to rename `Uniform Resource Locator`; those strings differ, so the rename does not match. Prefer `.rename(columns=...)` over mutating `df.columns.values` directly. String operations apply to string-like data, not only one particular dtype name. To check missing values, use `.isna()` rather than `== np.nan`.

**Quiz extension:** explain which values a conversion would discard before using `errors="coerce"`, then show how to preserve recoverable values and count genuinely missing entries. A successful numeric conversion does not prove that the cleaned data are accurate.

---

<a id="study-section-8"></a>

## 8. Lecture 18c: recoding

Source notebook: [lecture_18c_recoding.ipynb](lecture_18_Merging_Cleaning_Recoding/lecture_18c_recoding.ipynb)

**Purpose:** turn existing measurements or categories into a more useful representation. Cleaning corrects recorded information; recoding creates a different representation for analysis. Keep the original variable when useful.

#### New tools and patterns

| Tool | Example | Use |
|---|---|---|
| Loop with branches | `if lat <= 0: ...` | Explicitly classify observations |
| Scalar function + Series `.apply` | `circuits["lat"].apply(check_hemisphere)` | Reuse one classification rule for every entry |
| `pd.cut` | `pd.cut(s, bins=edges, labels=names)` | Convert numeric measurements into interval categories |
| `right=True` | Default right-closed bins | Interval `(a, b]`: excludes a, includes b |
| `np.inf`, `-np.inf` | `[-np.inf, 45, 145, np.inf]` | Cover all finite values without guessing extrema |
| Dictionary lookup | `census_region["Georgia"]` | Convert a known key to its mapped category |
| Dictionary membership | `state in census_region` | Check whether a lookup is safe |
| `.replace(mapping)` | `states.replace(census_region)` | Replace known values, leaving unmatched values unchanged |
| `.value_counts()` | `df["category"].value_counts()` | Count observations in each category |

#### Three ways to implement the same rule

The hemisphere rule in this lecture is South for latitude <= 0 and North for latitude > 0. That is the exercise's convention for the equator.

```python
hemispheres = []
for lat in circuits["lat"]:
    if lat <= 0:
        hemispheres.append("South")
    else:
        hemispheres.append("North")
circuits["hemisphere_loop"] = hemispheres

def check_hemisphere(lat):
    if lat <= 0:
        return "South"
    return "North"

circuits["hemisphere_apply"] = circuits["lat"].apply(check_hemisphere)
circuits["hemisphere_cut"] = pd.cut(
    circuits["lat"], [-np.inf, 0, np.inf], labels=["South", "North"]
)
```

The loop makes the algorithm explicit; `.apply` packages a reusable scalar rule; `cut` states the numeric boundaries directly. The loop and apply examples assume latitude is valid and nonmissing. A missing float compares false to `<= 0`, so an unguarded else would incorrectly label it North; `cut` preserves missingness. Add a missing-value check if your data require it.

#### Useful code block: altitude groups and boundary checks

```python
circuits["altitude_group"] = pd.cut(
    circuits["alt_numeric"],
    bins=[-np.inf, 45, 145, np.inf],
    labels=["Low altitude", "Medium altitude", "High altitude"],
    right=True
)
```

45 is Low, 145 is Medium, and values greater than 145 are High. There must be one fewer labels than edges. Values outside the supplied intervals become missing, as do missing inputs.

With `bins=[0, 120, 400, 800]`, zero is excluded, negatives are excluded, and values above 800 are excluded. With `bins=[-90, 0, 90]`, latitude -90 is excluded. `include_lowest=True` can include the first finite left endpoint; infinite endpoints can cover all finite values, but do not validate whether an impossible latitude such as 100 is sensible.

#### Useful code block: dictionary-based categories with a fallback

```python
def get_region(state):
    if state in census_region:
        return census_region[state]
    return "Not a state"

pres_state["region"] = pres_state["state"].apply(get_region)
pres_state["region_2"] = pres_state["state"].replace(census_region)
```

For an unmapped `United States` row, the first result is `Not a state`; the second remains `United States`. They implement different fallback policies. A useful extension is `.map(mapping)`, which produces missing values for unmatched keys instead of leaving them unchanged.

#### What all the exercises apply

1. **Party loop:** classify DEM as Democratic, REP as Republican, and everything else as Other. This is many-to-one recoding.
2. **Party function:** implement the same rule with `.apply`, query disagreements between the two result columns, and count categories. Checking agreement verifies the implementations behave alike.
3. **County size:** use vote bins Small <= 10,000, Medium <= 100,000, and Large above that. A first edge of zero excludes zero votes; starting at `-np.inf` includes them. Inspect missing results rather than assuming every row was classified.
4. **Regions:** look up state names, handle the aggregate row explicitly, and compare custom fallback behavior with `.replace`.
5. **House margins:** select winners, merge district vote totals, calculate `win_pct`, then cut at 55 and 65 into Close, Comfortable, and Landslide. Inspect a missing result's inputs: undefined percentages cannot be repaired by relabeling them.
6. **Census divisions:** apply a state-to-division lookup to every county, query specific divisions, count rows, and inspect the first ten New England counties. State names repeat because one row represents a county, not a state.

**Pitfalls:** the provided altitude exercise stores altitude labels in a column called `hemisphere`; use a distinct descriptive name such as `altitude_group` so you do not overwrite the geographic category. Call `.apply(check_hemisphere)`, not `.apply(check_hemisphere())`. The function must return its label; printing it is not enough.

**Quiz extension:** trace inputs exactly on every boundary and outside the range. These are the cases that distinguish `>`, `>=`, and the interval settings.

---

<a id="study-section-9"></a>

## 9. Lecture 19: grouping and aggregation

Source notebook: [Lecture19.ipynb](Lecture_19_grouping_aggregating/Lecture19.ipynb)

**Purpose:** summarize observations by group, rank those summaries, and attach group-level information back to individual rows. Always identify the unit of a row before and after aggregation.

#### New tools

| Tool | Example | Output / meaning |
|---|---|---|
| `.groupby` | `df.groupby("driverId")` | A GroupBy object describing groups |
| Select one grouped column | `groups["points"]` | SeriesGroupBy |
| Grouped `.mean` | `groups["points"].mean()` | Series with one mean per group |
| `.agg` with function names | `s.agg(["mean", "std", "min", "max"])` | Several summary statistics |
| `.agg` with a dict | `df.agg({"A": ["sum", "min"], "B": ["max"]})` | Different summaries by column |
| Named grouped aggregation | `.agg(mean_points=("points", "mean"))` | Names an output column and specifies its input and operation |
| Function object | `.agg(appearances=("points", len))` | Calls a supplied function on each grouped Series |
| Several group keys | `.groupby(["raceId", "constructorId"])` | One group per distinct key combination |
| `axis="columns"` | `df.agg("mean", axis="columns")` | Summarizes across columns separately for each row |
| Grouped merge | `pd.merge(df, summary, on="driverId", how="left")` | Attaches a group's statistics to each detailed row |

#### Useful code block: rank mean points by driver

```python
top_five = (df_results.groupby("driverId")["points"]
            .mean()
            .sort_values(ascending=False)
            .iloc[:5])
```

The chain goes DataFrame → DataFrameGroupBy → SeriesGroupBy → Series → Series → Series. The output index contains **driver IDs**, not consecutive rank numbers. `.iloc[0]` means the first result by position; `.loc[driver_id]` means that particular driver's result. To get driver names, merge the ID with the drivers table.

`.groupby` alone does not compute the desired summary: choose which measurements and which operations. This “split, apply, combine” process splits rows into groups, applies a summary to each group, and combines those results.

#### Useful code block: several named summaries

```python
drivers_agg = (df_results.groupby("driverId")
    .agg(mean_points=("points", "mean"),
         sd_points=("points", "std"),
         min_points=("points", "min"),
         max_points=("points", "max"),
         appearances=("points", len)))
```

Each keyword is the new column name. Each tuple gives the input column and summary function. `"mean"` is a recognized operation name; `len` is the actual built-in function. A custom `count_unique(col)` must likewise be passed as a function object, not as the invented string `"count_unique"` and not called with empty parentheses.

For the grouped version, the result is a DataFrame whose ordinary columns are the named statistics and whose index is driverId. The ungrouped `df.agg(new_name=(column, operation), ...)` examples in this notebook have a different layout: the output names label rows and the source columns remain columns. Inspect `.index`, `.columns`, and `type` rather than assuming every form of `.agg` has one output shape.

#### Missing values and counting

`len(group)` and group `.size()` count rows, including those with missing measurements. `.count()` counts nonmissing entries in the selected column. `.nunique()` normally excludes missing values, while `len(pd.unique(s))` can count a missing entry as one distinct value. Choose the statistic that matches the question.

Pandas `.std()` uses a sample standard deviation by default; a one-observation group has missing sample standard deviation. NumPy `np.std` in the earlier lab uses a different default denominator. A mean or sum across groups may give different conclusions because a driver with more appearances has more opportunities to earn points.

#### Useful code block: multiple keys and a filtered population

```python
teamrace_agg = (df_results.query("raceId >= 500")
    .groupby(["raceId", "constructorId"])
    .agg(mean_points=("points", "mean"),
         cars_entered=("points", len)))
```

One row now represents a constructor in a particular race. The result has a MultiIndex containing the two identifiers. Filter **before** aggregation when only those observations should contribute; filter **after** aggregation when the condition concerns a computed statistic.

#### Useful code block: merge summaries back to observations

```python
merged = pd.merge(df_results, drivers_agg, on="driverId", how="left")
merged["above_driver_average"] = merged["points"] - merged["mean_points"]

team_summary = teamrace_agg.reset_index()
with_teams = pd.merge(
    df_results, team_summary,
    on=["raceId", "constructorId"], how="left"
)
```

The summary key can be an appropriately named index level, as the notebook demonstrates. Resetting the index makes the matching keys ordinary columns and is often easier to inspect. Since the team summary was filtered to race IDs >= 500, earlier detailed rows have no summary match and get missing summary values.

#### What all the exercises apply

- **Per-race summaries:** group by `raceId` and aggregate mean laps and mean points. One output row is one race.
- **Constructor ranking:** group by `constructorId`, compute mean points, and sort descending.
- **Scatter plot after merging:** put a driver's mean points on x and an individual result's points on y. A vertical difference reflects one result's deviation from that driver's mean.
- **Team-race merge:** match on both raceId and constructorId. Using constructorId alone attaches summaries from other races and multiplies rows.

**Other reusable ideas:** `zip` pairs corresponding elements lazily and its iterator is consumed; `range` represents a sequence without storing every integer but can be iterated again. Printing either is not the same as enumerating its values. GroupBy similarly needs an operation to produce the table you want.

**Quiz extensions:** distinguish row counts from nonmissing counts, totals from averages, the first sorted position from an identifier label, and statistics calculated before versus after filtering. If you want five whole summary rows, sort the summary DataFrame rather than only a detached column.

---

<a id="study-section-10"></a>

## 10. Lecture 20: JSON and nested data

Source notebook: [Lecture20_JSON.ipynb](Lecture_20_JSON/Lecture20_JSON.ipynb)

**Purpose:** turn structured text into Python objects, navigate nested dictionaries and lists, handle absent information, and choose a useful row structure for tabular analysis. This lecture reads already downloaded API responses; it does not yet require making live web requests.

#### JSON and Python equivalents

| JSON | Python after parsing |
|---|---|
| Object `{...}` | `dict` |
| Array `[...]` | `list` |
| String | `str` |
| Number | Usually `int` or `float` |
| `true`, `false`, `null` | `True`, `False`, `None` |

JSON is a text format, not a Python dictionary. JSON requires double-quoted strings and keys, lowercase Boolean/null literals, and no ordinary Python comments or trailing commas.

#### New functions and methods

| Tool | Example | Use |
|---|---|---|
| `json.loads` | `player = json.loads(player_text)` | JSON string → Python object |
| `json.dumps` | `text = json.dumps(player, indent=4)` | Python object → JSON string |
| `json.load` | `player = json.load(file)` | Read JSON from an open file into Python |
| `json.dump` | `json.dump(player, file, indent=4)` | Write JSON to an open file |
| `open` | `open(path, "r", encoding="utf-8")` | Open a file for reading; `"w"` opens for writing |
| `with` | `with open(...) as file:` | Close the file when the block ends, including on errors |
| File `.read()` | `text = file.read()` | Read file contents as a string |
| `.keys()` | `stats.keys()` | Inspect the current dictionary level's keys |
| `.get()` | `stats.get("chess_daily", {})` | Return a value or a default if a key is absent |
| `in` | `"accuracies" in game` | Check whether a dictionary key exists |
| `pd.DataFrame` | `pd.DataFrame(rows)` | List of row dictionaries → table |
| `pd.to_datetime` | `pd.to_datetime(s, unit="s")` | Unix seconds → date/time values |
| `.astype` | `df["points"].astype(float)` | Explicit conversion when values are valid |
| `.isna().sum()` | `df["forename"].isna().sum()` | Count unmatched or otherwise missing values |

The `s` in loads/dumps means **string**. The functions without `s` receive a file object, not a path string.

#### Useful code block: loading, saving, and checking types

```python
with open("chess_raw_data/stats_hikaru.json", "r", encoding="utf-8") as file:
    stats = json.load(file)

print(type(stats), stats.keys())
print(stats["chess_blitz"]["last"]["rating"])

with open("my_players.json", "w", encoding="utf-8") as file:
    json.dump({"favorite": "Hikaru"}, file, indent=4)
```

Writing with `"w"` replaces the destination's existing contents, so choose the output path deliberately. `indent` only affects readability. `file.closed` is true after the with block; the loaded dictionary remains available.

`json.loads` raises `json.JSONDecodeError` on invalid JSON. Integer Python dictionary keys become strings in JSON, so a JSON round trip does not necessarily preserve every Python type exactly.

#### Navigating nested structure

```python
archive["games"][0]["white"]["username"]
stats["chess_blitz"]["record"]["win"]
```

Read left to right: dictionary key → list index → dictionary key → dictionary key. Use `type`, `.keys()`, `len`, and small slices while exploring. `len(dict)` counts keys at that level; `len(list)` counts list elements. Not every value in the same outer dictionary must have the same type.

#### Useful code block: absent keys without crashing

```python
for time_control in ["chess_bullet", "chess_blitz", "chess_rapid", "chess_daily"]:
    if time_control in stats:
        print(time_control, stats[time_control]["last"]["rating"])
    else:
        print(time_control, "never played")

accuracy = game.get("accuracies", {}).get("white")
```

Indexing an absent key with `[...]` raises `KeyError`. `.get(key)` defaults to `None`; providing `{}` as the fallback supports another dictionary `.get`. This last example assumes an existing `accuracies` value is a dictionary. A present key whose value is explicitly `None` is a different case and does not use the missing-key default.

Putting one `try/except` around the whole loop stops that loop at the first error. Checking each record individually lets later records continue.

#### Useful code block: one row per game

```python
def game_to_row(game):
    accuracies = game.get("accuracies", {})
    return {
        "url": game["url"],
        "end_time": game["end_time"],
        "time_class": game["time_class"],
        "white_username": game["white"]["username"],
        "white_rating": game["white"]["rating"],
        "white_result": game["white"]["result"],
        "white_accuracy": accuracies.get("white"),
        "black_username": game["black"]["username"],
        "black_rating": game["black"]["rating"],
        "black_result": game["black"]["result"],
        "black_accuracy": accuracies.get("black"),
    }

rows = []
for game in archive["games"]:
    rows.append(game_to_row(game))
df_games = pd.DataFrame(rows)
df_games["end_time"] = pd.to_datetime(df_games["end_time"], unit="s")
print(df_games["time_class"].value_counts())
```

Every dictionary in `rows` becomes one observation, and its keys become columns. Choose this unit deliberately. `pd.DataFrame([profile])` makes one profile one row; `pd.DataFrame(profile)` can fail for all-scalar dictionaries or use list-valued entries to determine an unintended row count. Wrapping the dict is significant.

For player statistics, the lecture instead chooses **one row per player per time control**. `stats_to_rows` extracts current rating, best rating, rating date, uncertainty, wins, losses, and draws into a list of row dictionaries. Combining the returned lists across players creates one rectangular table. `all_rows.extend(new_rows)` is an equivalent useful way to add each returned row; `.append(new_rows)` would create a nested list.

After flattening, earlier Pandas tools apply: add games and win percentages, group by time class, aggregate mean ratings and total games, query Blitz players, sort, and draw a horizontal bar plot. `unit="s"` matters because Unix timestamps here count seconds, not the default date-time unit.

#### What all the exercises apply

1. **Inspect an archive:** load a file using the lowercased username, check outer keys, count `archive["games"]`, and navigate the first game's white username and black rating.
2. **Missing accuracies:** loop over games and increment a counter when `"accuracies" not in game`. Count missing keys rather than assuming every record has identical structure.
3. **Flatten games:** use the function pattern above, preserve absent accuracies as `None`, convert timestamps, show five rows, and count time classes.
4. **F1 nested response:** follow `f1_race["MRData"]["RaceTable"]["Races"][0]`, then iterate `race["Results"]`. Extract driver and constructor IDs from their nested dictionaries. Convert grid/position to integers and points to floats because this API stores them as strings.
5. **Combine JSON and CSV:** rename API `driverId` to `driverRef`, left-merge driver names/nationality from the CSV, and count missing names. A driver absent from an older CSV is a legitimate unmatched record, not necessarily a broken merge.

**Quiz extensions:** trace the type at each nesting level; distinguish an absent key from an empty list or a null value; explain why a list of dictionaries is a natural table input; and combine JSON extraction with cleaning, merging, and grouping. If a denominator such as games played can be zero, decide how to represent the undefined percentage.

---

<a id="study-section-11"></a>

## 11. Lab 5: argument conventions (supporting prerequisite)

Source notebook: [Lab_05.ipynb](Lab_05/Lab_05.ipynb)

**Purpose:** read function signatures and design functions that accept explicit options or variable numbers of arguments. These ideas reappear in `set_strategy(..., floor=True)`, plotting options, and named aggregation.

| Syntax | What it means | What the function receives |
|---|---|---|
| `def f(x, *, color="red")` | Parameters after the bare star must be named | `f(3, color="blue")` works; passing blue positionally does not |
| `def f(*args)` | Collect positional arguments | A tuple, possibly empty |
| `def f(**kwargs)` | Collect otherwise unmatched keyword arguments | A dictionary, possibly empty |
| `print(a, b, sep="-")` | Change an explicit keyword-only print setting | Prints a and b with a hyphen between them |
| `len(args)` | Count received positional inputs | Useful before indexing |
| `key in kwargs` | Check whether an option exists | Prevents a missing-key lookup |

#### Exercises 1–2: control how a function is called

```python
def birthday(month, day, *, name=None):
    if name is None:
        print(f"Your birthday is {month} {day}.")
    else:
        print(f"{name}, your birthday is {month} {day}.")

birthday("June", 20, name="Peter")
birthday(day=20, name="Peter", month="June")
```

Only name is required to be keyword-supplied in this signature. Month and day can be positional or keyword arguments. The first four calls in Exercise 1 work; `birthday(month="June", 20, name="Peter")` is invalid syntax because this ordinary positional argument follows a keyword argument. To require names for every input, use `def birthday(*, month, day, name=None):`.

A keyword-only rule controls the **calling convention**, not the value's type. It does not itself reject `name=1989`.

#### Variable positional inputs and safe indexing

```python
def show_fourth(*args):
    if len(args) >= 4:
        print(args[3])
    else:
        print("Not enough arguments")

show_fourth("a", 20, [1, 2], "fourth", 99)
show_fourth()
```

Arguments can have different types. With no arguments, args is `()`. The fourth entry is index 3, not index 4. Without the length check, too few arguments raise `IndexError`.

The plotting example uses the number of inputs to choose an operation: one input gives histogram values, two give bar categories and heights, and other counts produce a message and an early return. A histogram counts data into bins; a bar chart uses heights you explicitly supplied. Returning early avoids displaying a meaningless empty plot.

#### Keyword options and branching

```python
def student_info(**kwargs):
    if "name" not in kwargs:
        print("Hi!")
    elif "major" in kwargs:
        print(f"Hi, I'm {kwargs['name']} and I'm majoring in {kwargs['major']}!")
    else:
        print(f"Hi, I'm {kwargs['name']}!")
```

Keyword names become string dictionary keys. With no keyword arguments, kwargs is `{}`. This function accepts extra keys but ignores them; that behavior follows from its implementation and is not a promise made by all library functions. Having `name=None` is different from the key being absent.

```python
def make_bar_plot(categories, heights, **kwargs):
    if "color" in kwargs:
        plt.bar(categories, heights, color=kwargs["color"])
    else:
        plt.bar(categories, heights)
    if "grid" in kwargs and kwargs["grid"]:
        plt.grid()
    plt.show()
```

The grid condition checks both presence and truth: merely passing `grid=False` must not enable a grid. Here `and` short-circuiting is helpful because it prevents indexing an absent key. In the Student meeting simulation, short-circuiting two mutating method calls is harmful because both meetings must be recorded.

#### Print and final-check answers

`print("Port", "au", "Prince, Haiti", sep="-")` produces `Port-au-Prince, Haiti`, typing the hyphen once. `sep="*"` controls separation; passing `"*"` as another positional argument prints it as another item.

`f1(*, x, y)` demonstrates keyword-only parameters; `f2(*values)` collects positional arguments into a tuple; `f3(**options)` collects keywords into a dictionary. The names args and kwargs are conventions; the stars determine the behavior.

**Quiz extension:** stars also unpack values at a call site. `f(*values)` supplies a sequence as separate positional arguments; `f(**options)` supplies a dictionary as keyword arguments. Do not confuse collecting inputs in a definition with unpacking inputs in a call.

---

<a id="study-section-12"></a>

## 12. Lab 6: simulation and the interactive game (supporting prerequisite)

Source notebook: [Lab_06.ipynb](Lab_06/Lab_06.ipynb)

**Purpose:** compare a strategy's typical outcome with its variability, then turn a simulated decision process into an interactive program. This lab relies on the Assignment 4 trial function returning `(chosen_name, chosen_skill)`.

| Tool | Example | Use |
|---|---|---|
| `np.mean` | `np.mean(chosen_skills)` | Typical outcome across trials |
| `np.std` | `np.std(chosen_skills)` | Spread of individual outcomes |
| `np.arange` | `np.arange(num_candidates)` | Numeric strategy positions for plotting |
| `plt.bar` | `plt.bar(strategies, mean_skills)` | Compare one mean per strategy |
| `plt.hist` | `plt.hist(chosen_skills, bins=20)` | Show the distribution of individual outcomes |
| `input` | `input("pick or pass? ")` | Obtain user input as text |
| `.strip().lower()` | `answer.strip().lower()` | Normalize whitespace and capitalization |
| `while True`, `break` | Reprompt until input is valid | Separate invalid answers from real game decisions |
| `return name, skill` | Finish immediately after selection | Exit the whole function, not just its innermost loop |

#### Part 1, Exercises 1–5: summarize and visualize trials

For every N, run 100 trials, collect selected skills, and append their average to mean_skills. The bar plot uses one x position per strategy and its corresponding estimated mean as height. The helper `labeled_bar_plot` packages drawing and labeling; it does not calculate the trial outcomes.

For N=8, collect the individual outcomes instead. A mean tells you the typical level, while standard deviation tells you how variable individual trials are. A histogram exposes features hidden by the mean, including lower-skill outcomes caused by reaching the forced last-candidate choice.

```python
chosen_skills = []
for trial in range(100):
    name, skill = run_one_trial(8)
    chosen_skills.append(skill)
spread = np.std(chosen_skills)
plt.hist(chosen_skills, bins=20)
plt.xlabel("Chosen partner skills")
plt.ylabel("Number of trials")
plt.title(f"N = 8, standard deviation = {spread:.2f}")
plt.show()
```

Increasing trials to 1,000 makes the estimated means and histogram shape steadier. It does not eliminate the underlying randomness of one interview sequence. A roughly flat high-performing range can make the identity of the numerical winner unstable even when the overall conclusion is stable.

**Extension:** outcome standard deviation is different from uncertainty in an estimated mean. For independent trials, a rough standard error of that mean is outcome standard deviation divided by the square root of the number of trials. More trials shrink estimation uncertainty; they do not force individual outcomes to become similar.

#### Part 2: the game and all three follow-up exercises

Shuffle candidate names, show each name and skill, accept a pick/pass decision, and force a final choice if necessary. Normalize input before checking it. Invalid text should repeat the same question without advancing to the next candidate.

```python
def play_lab_partner_game():
    order = list(lab_partners.keys())
    random.shuffle(order)
    best_seen = 0
    for index, name in enumerate(order):
        skill = lab_partners[name]["lab_skills"]
        best_seen = max(best_seen, skill)
        print(f"Interview {index + 1}: {name}, skill {skill}, best seen {best_seen}")
        if index == len(order) - 1:
            print("Final candidate: selected automatically")
            return name, skill
        while True:
            answer = input("pick or pass? ").strip().lower()
            if answer in ("pick", "pass"):
                break
            print("Please enter pick or pass")
        if answer == "pick":
            print(f"Selected after {index + 1} interviews")
            return name, skill
```

1. Play five games, record their chosen skills, and compare their average with five simulated trials at N=18. Five observations are a noisy comparison, so a small difference is weak evidence.
2. Add the running best and number seen. The example above includes both. Be precise: the number interviewed before selection is not automatically the strategy's observation-only N; a benchmark strategy can finish at many later interviews after it completes its N observations.
3. Accept `Pick`, `PICK`, and surrounding spaces by normalization. If accepting abbreviations, define them unambiguously: `p` cannot clearly mean both pick and pass. Rejecting or clarifying ambiguous shorthand is better than guessing a decision.

**Quiz connection:** practice identifying which loop `break` exits, why `return` exits the whole game, and why resetting lists at the right nesting level matters. User-input text stays a string until explicitly converted.

---

<a id="study-section-13"></a>

## 13. Lab 7: regression with classes

Source notebook: [Lab_07.ipynb](Lab_07/Lab_07.ipynb)

**Purpose:** store the state of a fitted model so its learned coefficients can be reused for predictions. Regression is linear in its unknown coefficients even when the input functions include sine, cosine, or x squared.

#### New functions, operators, and attributes

| Tool | Example | Meaning |
|---|---|---|
| `np.linspace` | `np.linspace(-np.pi, np.pi, 20)` | Evenly spaced input observations |
| Functions as values | `[f_0, f_1, f_2]` | A list of basis functions, not their evaluated outputs |
| `np.zeros` | `np.zeros((m, n))` | Allocate the design matrix |
| Column selection | `A[:, k] = f(x)` | Fill basis-function column k for all observations |
| `.T` | `A.T` | Matrix transpose |
| `@` | `A.T @ A` | Matrix multiplication, unlike element-wise `*` |
| `la.solve` | `la.solve(A.T @ A, A.T @ y)` | Solve the normal equations for fitted coefficients |
| `np.sum` | `np.sum((predicted-y)**2)` | Sum of squared errors |
| `rng.standard_normal` | `rng.standard_normal(y.shape)` | Random noise with matching shape |
| Model `.fit` | `model.fit(x, y)` | Store data and learn coefficients |
| Model `.predict` | `model.predict(new_x)` | Use learned coefficients on chosen inputs |

#### Design matrix and least squares

The initial data use `y = -1 + 2*cos(x) + sin(x) + noise`. The three basis functions are 1, cos(x), and sin(x). With m observations and n basis functions, A has shape `(m, n)`, coefficients have n entries, and A times coefficients gives m fitted values.

The normal equations are `(A.T @ A) @ coefficients = A.T @ y`. The left matrix is n×n; the right vector has n entries. Solving chooses coefficients minimizing the squared residual sum when the needed system is nonsingular. Noise explains why fitted coefficients differ slightly from -1, 2, and 1.

#### Useful code block: the reusable model

```python
class OrdinaryLeastSquares:
    def __init__(self, function_list):
        self.f_list = function_list
        self.coefficients = None
        self.x = None
        self.y = None

    def fit(self, x, y):
        self.x = x
        self.y = y
        A = np.zeros((len(x), len(self.f_list)))
        for k in range(len(self.f_list)):
            A[:, k] = self.f_list[k](x)
        self.coefficients = la.solve(A.T @ A, A.T @ y)

    def predict(self, x):
        assert self.coefficients is not None, "fit the model first"
        y_pred = np.zeros(x.shape)
        for k in range(len(self.f_list)):
            y_pred += self.coefficients[k] * self.f_list[k](x)
        return y_pred
```

Import numpy as np and numpy.linalg as la first. These methods assume matching numeric training arrays and NumPy-array prediction inputs, as the exercise specifies. `fit` mutates model state and returns None; `predict` returns an array. `self.f_list[k]` retrieves a function, then `(x)` calls it. A constant function returning 1.0 broadcasts into a whole design-matrix column.

#### Quadratic application

Use `[lambda x: 1.0, lambda x: x, lambda x: x**2]` to fit `a + b*x + c*x**2`. The same fitting algorithm works; only the basis functions change. Passing `f(x)` instead of f in the constructor would store evaluated numbers or arrays in place of callables.

#### SimpleLinearRegression and R-squared

The child model fixes the functions to constant and identity, then inherits fit and predict. The coefficient ordering is intercept followed by slope. Lab 8's `super()` gives a concise way to reuse the parent initialization.

```python
class SimpleLinearRegression(OrdinaryLeastSquares):
    def __init__(self):
        super().__init__([lambda x: 1.0, lambda x: x])
        self.r_squared = None

    def compute_r_squared(self):
        assert self.x is not None, "fit the model first"
        dx = self.x - np.mean(self.x)
        dy = self.y - np.mean(self.y)
        s_xx = np.sum(dx**2)
        s_yy = np.sum(dy**2)
        s_xy = np.sum(dx * dy)
        assert s_xx > 0 and s_yy > 0, "R-squared needs variation here"
        self.r_squared = s_xy**2 / (s_xx * s_yy)
        return self.r_squared
```

This form recalculates when called, avoiding a stale cached value after refitting. The original lab caches the value, so a reusable implementation with that cache would need to clear it when fitting new data. The notebook's displayed mean-y formula mistakenly sums x_i; the code correctly uses the mean of y.

For x=`[1,2,3]` and y=`[0,3,2]`, the intercept is -1/3, slope 1, and R²=3/7, approximately .4286. In simple least squares with an intercept, R² measures explained variation and is the square of the sample correlation. Do not assume that same formula describes arbitrary multiple-basis models.

#### Last application and useful extensions

The final example regresses estimated optimal floors on student skills. It turns noisy simulation outputs into an approximate decision rule. It does not prove the rule remains best if the population changes its behavior.

Repeated or dependent basis columns can make `A.T @ A` singular; constant x also prevents estimating a unique line. A noiseless straight-line example should recover its intercept/slope and predict exactly up to floating-point tolerance. Use `np.allclose` rather than requiring exact equality for numerical results.

**Quiz connection:** distinguish model construction, state-changing training, and value-returning prediction. Explain matrix shapes and why the child inherits working fit/predict methods without copying them.

---

<a id="study-section-14"></a>

## 14. Lab 8: method chaining and super

Source notebook: [Lab_08.ipynb](Lab_08/Lab_08.ipynb)

**Purpose:** understand what every step returns, then reuse a parent's initialization and methods without copying them.

#### Chaining depends on returned objects

```python
top5 = (df_results.query("raceId >= 500")
        .groupby("constructorId")["points"]
        .mean().sort_values(ascending=False).iloc[:5])
```

After query the result is a DataFrame; after groupby and selecting points it is a SeriesGroupBy; mean, sorting, and iloc produce Series here. A chain need not return the same type at every step. The next operation must be valid for the previous return value.

Original `model.fit(...)` stores coefficients but returns None. Thus `model.fit(...).predict(...)` attempts to call predict on None and fails. Add `return self` to fit if you want the fitted model back for chaining. This does not create a copy.

#### super() and inheritance

`super().__init__([f_0, f_1])` initializes this same subclass instance with the parent's logic. It does not build a separate parent object. You do not supply self manually. Once you define a child initializer, parent initialization is not automatic.

The ModelBuilder parent initializes a per-instance features list and hyperparameters dictionary. Its add_feature, set_hyperparameter, and show methods return self. The subclass adds a learned rule and training accuracy.

#### All four DecisionTreeBuilder methods

The following methods belong inside `DecisionTreeBuilder(ModelBuilder)` alongside the supplied train method.

```python
def __init__(self):
    super().__init__()
    self.set_hyperparameter("min_samples", 1)
    self.rule = None
    self.train_accuracy = None

def add_feature(self, feature):
    if feature in self.features:
        print(f"'{feature}' already added, skipping")
    else:
        super().add_feature(feature)
    return self

def predict(self, row):
    assert self.rule is not None, "train a valid rule first"
    col, threshold = self.rule
    return 1 if row[col] > threshold else 0

def show(self):
    super().show()
    if self.rule is None:
        print("Not trained yet (or no valid split found)")
    else:
        col, threshold = self.rule
        print(f"Learned rule: predict 1 if {self.features[col]} > {threshold}")
        print(f"Training accuracy: {self.train_accuracy}")
    return self
```

The constructor must return None implicitly; it is not a chainable configuration method. Add_feature must return self both for a new feature and for a skipped duplicate. Predict deliberately ends a chain by returning the requested classification, rather than self.

#### Understand the supplied training loop

This model is one yes/no split, often called a decision stump. A feature is an input measurement, such as hours studied. A hyperparameter is a user-chosen setting, such as min_samples. A learned threshold is selected from the data during training.

Train loops through feature indices, then sorted distinct observed thresholds. `set(...)` removes duplicates. `sum(row[col] > threshold for row in rows)` counts how many examples lie above a threshold. Reject a split with fewer than min_samples on either side using continue. Create predicted labels and use zip plus a Boolean sum to calculate the proportion correct. Retain the candidate only if its accuracy is greater than the previous best, so the first candidate wins a tie. Store `(col, threshold)` as a tuple and return self.

#### All test results and check-your-understanding answers

| Test | Chosen rule | Training accuracy | Prediction for `[4,4]` |
|---|---|---|---|
| 1 | hours_studied > 3 | 1.0 | 1 / pass |
| 2 | hours_slept > 5 | 1.0 | 0 / fail |
| 3, min_samples=3 | hours_slept > 7 | 5/6 | 0 / fail |

The perfect rule from Test 2 leaves only two examples on one side, so Test 3 forbids it. Equality with the threshold predicts 0 because the test is strictly greater than.

If add_feature returns None when adding a new feature, the second call in the first feature chain fails with a NoneType AttributeError. If the child omits `super().__init__()`, setting the default hyperparameter encounters a missing hyperparameters attribute. A number is the right return value for predict because prediction is its requested result.

**Quiz extensions:** explain why a missing return on only one branch causes input-dependent chain failures. Distinguish training accuracy from performance on new data. If no candidate split is valid, rule remains None; guard prediction instead of unpacking a nonexistent tuple. The supplied train loop assumes nonempty, matching rows and labels and valid feature positions.

---

<a id="study-section-15"></a>

## 15. Assignment 4: one-sided selection (supporting prerequisite)

Source notebook: [fussy_lab_partner.ipynb](_assignments/assignment_04/fussy_lab_partner.ipynb)

**Purpose:** build a simulation from dictionary lookups, loops, a running maximum, a stopping rule, and repeated random trials. This is the foundation for Lab 6 and Lectures 15–16; the later mutual problem adds a second person's decision.

#### Exercises 1–4: represent, shuffle, observe, and choose

`lab_partners[name]["lab_skills"]` first selects a student's inner dictionary and then its skill. Use the same two-stage lookup for birthdays. Comparisons need three possibilities: greater, less, or tied. Shirley and Annie have equal skills in this input.

`list(lab_partners.keys())` gives candidate names. `random.shuffle(order)` mutates that list and returns `None`; do not assign its result back to order. Shuffling names keeps each person's attributes together because the dictionary remains the source of their information.

The strategy observes N candidates without selecting them, remembers their highest skill, then selects the **first later candidate strictly better than that fixed benchmark**. If none qualifies, it must select the final candidate. Initialize that fallback before searching so `chosen_name` is always defined.

#### Useful code block: one complete trial

```python
def run_one_trial(N):
    order = list(lab_partners.keys())
    assert 0 <= N < len(order), "N must be a valid observation count"
    random.shuffle(order)
    benchmark = 0
    for name in order[:N]:
        benchmark = max(benchmark, lab_partners[name]["lab_skills"])
    chosen_name = order[-1]
    for name in order[N:]:
        if lab_partners[name]["lab_skills"] > benchmark:
            chosen_name = name
            break
    return chosen_name, lab_partners[chosen_name]["lab_skills"]
```

N is an integer count in the intended calls. Starting the benchmark at zero relies on the supplied nonnegative skills; a rule designed for negative measurements needs a different initial bound. The second loop does not keep raising the benchmark. `break` is what makes this choose the first qualifier rather than the final qualifier.

N=0 observes nobody and chooses the first positive-skill candidate. N equal to the number of candidates minus one leaves only the final candidate to choose. A strict `>` rejects a tie with the benchmark.

#### Exercises 5–8: functions and return values

`beats_benchmark(skill, benchmark)` returns a Boolean, which can be written directly as `return skill > benchmark`. A function that builds an interview sentence should **return the string**, letting its caller decide whether to print it. The trial function returns a two-element tuple, so `name, skill = run_one_trial(10)` unpacks it.

Order, benchmark, and chosen_name are local variables created anew on each call. The function reads the surrounding lab_partners dictionary. Restarting a notebook removes that dictionary until its setup cell runs again.

#### Exercises 9–10: compare strategies with repeated trials

```python
strategies = list(range(len(lab_partners)))
mean_skills = []
for N in strategies:
    outcomes = []
    for trial in range(100):
        name, skill = run_one_trial(N)
        outcomes.append(skill)
    mean_skills.append(float(np.mean(outcomes)))
best_index = int(np.argmax(mean_skills))
print(strategies[best_index], mean_skills[best_index])
```

There are two loops for two distinct purposes: compare strategy values, and estimate each strategy over repeated random orders. Reset outcomes inside the outer loop so different strategies do not get mixed together. Keep the chosen strategy and its mean paired when tracking a running best.

**Quiz extensions:** `np.argmax` returns a position, not a strategy label. It equals N only when the list is indexed that way. Also distinguish maximizing expected skill from maximizing the probability of choosing the single best candidate; these are different objectives and need not favor the same strategy. A noisy numerical winner is not proof of a unique optimum.

---

<a id="study-section-16"></a>

## 16. Assignment 5: zip, safe returns, lambda, and apply

Source notebook: [Assignment5.ipynb](_assignments/assignment_05/Assignment5.ipynb)

The file is named Assignment5 but its heading says Assignment 4. This summary follows the file's actual exercises (a)–(e).

#### (a) Zip corresponding movie names and years

`zip(names, years)` produces corresponding pairs, not all combinations. Tuple unpacking gives one variable for each member of a pair. Use if/elif/else so each movie enters exactly one category: year <=1959, year <=1999, or year >=2000.

```python
for title, year in zip(movie_names, movie_years):
    if year <= 1959:
        pre_1960s.append(title)
    elif year <= 1999:
        latter_20th.append(title)
    else:
        modern_movies.append(title)
```

The second condition does not need to repeat year>=1960 because the first branch already excluded earlier years. Create the output lists before the loop. Zip stops at the shortest input, so mismatched lengths can silently leave entries unused.

#### (b) Zip four sequences and format a mean

```python
for name, a, b, c in zip(player_names, round1, round2, round3):
    average = (a + b + c) / 3
    print(f"{name}'s average score: {average:.2f}")
```

Each iteration describes one player across three rounds. Parentheses ensure the sum is divided by three. `:.2f` formats a numeric value with exactly two decimal places; it does not change the stored numeric average. Unlike a zip iterator, a range object can be traversed again.

#### (c) Check the quadratic discriminant before taking its square root

The discriminant is `b**2 - 4*a*c`. Negative discriminant means no real roots. Check that before `np.sqrt` to avoid the invalid-square-root warning. Returning the string `"No real roots"` meets this part's requested output, but callers must know the return type can vary between tuple and string.

#### (d) Validate the leading coefficient and preserve the output shape

With a=0, division by `2*a` is invalid for the quadratic formula. Printing a warning does not by itself return two usable values. A bare return or an omitted return gives `None`, which cannot be unpacked into two roots.

```python
def quadratic_formula_safe(a, b, c):
    if a == 0:
        print("*** Warning: the leading coefficient must be non-zero")
        return None, None
    discriminant = b**2 - 4*a*c
    if discriminant < 0:
        print("No real roots")
        return None, None
    sqrt_d = np.sqrt(discriminant)
    return (-b + sqrt_d)/(2*a), (-b - sqrt_d)/(2*a)
```

This teaching version always returns a pair, including unsuccessful cases, so `root1, root2 = ...` is safe. Test for None before trying arithmetic on missing roots. The stored notebook already returns `(None, None)` for a=0, so its later text claiming that case necessarily fails is stale. This is a reminder to inspect actual code rather than assuming every comment is current.

#### (e) Lambda, Series.apply, Boolean counts, and Series names

```python
curve = lambda grade: round(10 * np.sqrt(grade), 1)
passed = lambda grade: grade >= 55
curved = student_info["numeric_grade"].apply(curve)
curved.name = "curved_grade"
before = student_info["numeric_grade"].apply(passed).sum()
after = curved.apply(passed).sum()
```

A lambda is an expression-based function. Pass the function object to apply; `curve()` would try to call it immediately without an argument. Apply returns a Series retaining row labels. A Boolean sum counts true cases because true contributes 1 and false contributes 0. This dataset has three passing grades before the curve and five after it.

Changing `curved.name` labels the Series; add `student_info["curved_grade"] = curved` if you want it stored as a DataFrame column. Nonnegative inputs are assumed for the square-root curve. `**` means exponentiation; `^` does not.

**Quiz connection:** Practice Quiz 4's speed conversion is the same apply pattern with a different scalar function. The important questions are what one input represents, what the function returns, and whether the caller expects a scalar, tuple, string, or Series.

---

<a id="study-section-17"></a>

## 17. Assignment 6: mutual selection and experiments

Source notebook: [assignment6_mutual_lab_partners.ipynb](_assignments/assignment_06/assignment6_mutual_lab_partners.ipynb)

**Purpose:** extend Student with useful operations and distinguish a method's state changes from its return value. In mutual selection, being willing to choose someone does not guarantee they choose you.

#### Exercises 1–5: the class additions

| Addition | Useful implementation | Purpose |
|---|---|---|
| `__repr__` | Return name and lab skills as a string | Readable students inside printed lists |
| Constructor assertions | Check `0 <= lab_skills <= 10` and `strategy >= 0` | Validate initial state, not only later setter calls |
| `__gt__` | `return self.lab_skills > other.lab_skills` | Compare students by skills using `>` |
| `strongest` | Keep a running best Student object | Return the person along with their attributes |
| `has_partner` | `return self.partner is not None` | Boolean presence check |
| `partner_skill` | Return None if unpaired, otherwise partner skills | Avoid calling methods on None |

These are methods except strongest, which is a function accepting a list. Readable representations must return a string; printing it inside `__repr__` is insufficient.

```python
def strongest(students):
    assert len(students) > 0, "need at least one student"
    best = students[0]
    for person in students[1:]:
        if person > best:
            best = person
    return best
```

Initialize from a real list member. The notebook's dummy Student("nobody", 0) can incorrectly win when every actual student also has skill zero. This version returns the first maximal student on a tie.

#### Exercises 6–8: why resetting matters

After one full semester, every student has a partner. A second call on the same state finds nobody searching and changes no partnerships. Copying the list is not enough, because a shallow copy contains references to the same Student objects.

```python
# Put this method inside Student.
def reset(self):
    self.benchmark = 0
    self.num_met = 0
    self.partner = None

# This is a separate function.
def reset_roster(students):
    for student in students:
        student.reset()
```

Preserve name, skills, and chosen strategy. Reset every student to clear both sides of all partnerships. After reset, random shuffling can produce different results on a later semester, although randomness does not guarantee that any particular person's partner changes.

The reset rule above belongs to this assignment's observation-based strategy. If adapting it to Lecture 16's fixed-floor strategy, preserve or reconstruct that intended floor; blindly resetting the benchmark to zero would erase it.

#### Exercise 9: why class-average partner quality cannot improve

Once everyone is paired symmetrically, each student appears exactly once as someone's partner. The partner-skill values are therefore a permutation of the class's own skills, and their mean is constant across pairing rules.

```python
def average_partner_skill(students):
    values = []
    for person in students:
        values.append(person.partner_skill())
    return float(np.mean(values))
```

This assumes a nonempty, fully paired roster. None is not a zero-skill partner and should not be silently counted as one. Constant overall mean does not mean the strategies have identical effects on individual students or inequality.

#### Exercise 10: one student's outcome given the population's strategy

```python
def outcome_for(sigma, sigma_aura, class_N, num_semesters):
    assert num_semesters > 0, "need at least one semester"
    students = build_roster(class_N)
    target = None
    for student in students:
        if student.get_name() == sigma:
            target = student
            break
    assert target is not None, "student not found"
    target.set_strategy(sigma_aura)
    outcomes = []
    for trial in range(num_semesters):
        reset_roster(students)
        run_semester(students, 12)
        outcomes.append(target.partner_skill())
    return float(np.mean(outcomes))
```

`class_N` controls everybody else's rule; `sigma_aura` is the target's observation count. Set the special strategy after creating the common roster. Resetting the assignment's dynamic state leaves that special strategy intact. Find and retain a reference to the actual target, rather than creating a new detached Student with the same name.

Repeat this for candidate strategies and compare means. `np.argmax` returns the position of the largest mean. Use the matching strategy list to turn that position into a strategy value. The notebook's high-trial-count results are specific to its population, deadline, objective, and other students' behavior, not universal constants.

**Quiz connection:** returning an object, using getters, validating state, safely following a partner reference, and mutating multiple related objects all transfer directly to the runner questions. A useful extension is to explain why changing all students' rules at once answers a different experiment from changing only one student's rule.

---

<a id="study-section-18"></a>

## 18. Assignment 7: complete Pandas workflows

Source notebook: [Assignment7.ipynb](_assignments/assignment_07/Assignment7.ipynb)

**Purpose:** combine Lecture 17's operations into complete small workflows. Each part uses a DataFrame as a table, a Series as one column, and scalars for values such as means or row counts. Dataset paths below assume this assignment's folder.

#### (a) Inspect and select

```python
df = pd.read_csv("data/features.csv")
print(type(df))
car_colnames = df.columns
df_cyl_wt = df[[car_colnames[1], car_colnames[4]]]
print(df.loc[10, "vehicle_id"])
df_block = df.iloc[20:30, 0:3]
```

The index displayed on the left is not a data column. car_colnames is a Pandas Index; choosing entries 1 and 4 gives labels for cylinders and weight in this file. A list of two labels selects a DataFrame. `.loc[10,...]` selects label 10, which coincides with position 10 in the freshly read default index. The block has ten rows and three columns because iloc excludes both stopping positions.

#### (b) Queries with a Python threshold

```python
fifa = pd.read_csv("data/fifa23_players_basic.csv")
threshold = 75
df_1 = fifa.query("Overall > @threshold")
df_2 = fifa.query("Overall <= @threshold")
print(f"{df_1['Age'].mean():.1f}")
print(f"{df_2['Age'].mean():.1f}")
```

The @ distinguishes the Python variable from a column name. `>` and `<=` put the boundary on exactly one side. Missing Overall values would be in neither subset, so complementary numeric inequalities partition all rows only when the relevant values are nonmissing. Numeric means normally skip missing ages.

#### (c) Sort and take the top five complete rows

`df_top_mileage = df.sort_values("mpg", ascending=False).iloc[:5, :]` keeps every feature of the five highest-mileage cars. Sorting only the mpg Series would lose the other information. Sorting does not renumber index labels, so use positional iloc to take the first five rows afterward.

#### (d) Backticks for column names, @ for Python values

```python
avg_height = fifa["Height(in cm)"].mean()
df_tall = fifa.query("`Height(in cm)` > @avg_height")
```

Backticks identify a column name containing punctuation or spaces; quotes delimit the entire query string. They serve different purposes. Do not write a string literal instead of referencing the column.

#### (e) Plot each year's distribution on one figure

```python
urban = pd.read_csv("data/wdi_urban.csv")
unique_years = pd.unique(urban["year"])
bins = range(0, 101, 10)
for years in unique_years:
    part = urban.query("year == @years")
    plt.hist(part["prop_urbanpopulation"], bins=bins,
             alpha=0.5, label=str(years))
plt.xlabel("Urban population (%)")
plt.ylabel("Number of countries")
plt.title("Urban population distributions by year")
plt.legend()
plt.show()
```

Filtering and drawing repeat; the common labels, legend, and show happen once afterward. Shared bin edges give comparable intervals. Alpha controls transparency; it does not normalize counts into proportions. Check the units of the recorded variable before choosing fixed bins.

#### (f) Transform currency, annualize, and filter

```python
def euro_to_usd(euros):
    return euros * 1.08

fifa["Wage(in USD)"] = fifa["Wage(in Euro)"].apply(euro_to_usd)
fifa["Annual Wage(in USD)"] = fifa["Wage(in USD)"] * 52
min_annual = 1_000_000
df_millionaires = fifa.query("`Annual Wage(in USD)` >= @min_annual")
print(len(df_millionaires))
```

1.08 is this exercise's stipulated conversion constant. Apply receives the function object and returns a Series. Multiplication by 52 is element-wise, so a second apply or Python loop is unnecessary. A weekly amount and an annual amount are different quantities; unit errors can produce valid Python but incorrect conclusions.

#### (g) Random assignment and group summaries

```python
np.random.seed(185)
fifa["GroupID"] = np.random.choice(["A", "B", "C", "D"], size=len(fifa))
for group in ["A", "B", "C", "D"]:
    part = fifa.query("GroupID == @group")
    print(f"{group}: {len(part)} players, average {part['Overall'].mean():.1f}")
```

The seed makes this particular sequence reproducible when the same calls run in the same order. Seed once before assignment, not inside a group loop. Each row has equal probability of each group; group sizes and mean ratings need not be exactly equal. `size` must match the number of rows rather than be typed as a fixed dataset-specific number.

**Quiz connection:** Practice Quiz 4 A–C reuse these steps with a two-way uniform split, a different positional block, and a speed-to-energy conversion. A function's name or the dataset's subject changes, but selecting rows, keeping return values, and preserving units remain the same.

---

<a id="study-section-19"></a>

## 19. Practice Quiz 4: A–F and answered extensions

Source notebook: [practice_quiz_4.ipynb](_quizzes/practice_quiz_4/practice_quiz_4.ipynb)

These notes explain A–F in both starter and solution versions. Keep the original setup cells and their class definitions in mind; they are part of the problem. Python examples below assume pandas as pd and numpy as np are imported and that paths are relative to this quiz's folder. Individual method definitions belong inside the indicated class.

| Question | Main pattern | Source concepts |
|---|---|---|
| A | Random column → complementary queries → percentages | Lecture 17, Assignment 7(g) |
| B | Positional rectangle → descending sort | Lecture 17 and index supplement |
| C | Scalar unit conversion → apply → element-wise formula | Assignment 5(e), Assignment 7(f) |
| D | Validate → mutate count → capped setter update | Lectures 14–15, Assignment 6 |
| E | Inherit → override → class-level setter | Lecture 16, Labs 7–8 |
| F | Build contained objects → nested search → symmetric links | Lectures 15–16 |

### A. Split a dataset into roughly 40% and 60%

```python
df_fifa = pd.read_csv("quiz_data/fifa23_players_basic.csv")
n = len(df_fifa)
df_fifa["random_var"] = np.random.uniform(0, 1, size=n)
groupA = df_fifa.query("random_var < 0.4")
groupB = df_fifa.query("random_var >= 0.4")
print(f"Group A: {100 * len(groupA) / n:.2f}%")
print(f"Group B: {100 * len(groupB) / n:.2f}%")
```

`pd.read_csv` returns a DataFrame. `len` returns its row count; `.shape[0]` is equivalent. Uniform draws provide one number per row. Since 40% of the interval lies below .4, the first group has probability .4, not a guaranteed exact share of rows. Assigning one random column and querying it twice makes membership consistent: every generated finite draw belongs to exactly one group.

Check `len(groupA) + len(groupB) == n`. Do not generate independent draws for the two filters: that can put one observation in both groups or neither. Counts divided by n give fractions; multiply by 100 for percentages. The supplied dataset is nonempty; an empty table would require handling division by zero.

**Variation:** for 20%, 50%, and 30%, use `u < .2`, `.2 <= u < .7`, and `u >= .7`. These boundaries cover the interval without overlaps. Increasing the sample size usually makes percentage estimates steadier, but does not guarantee exact quotas.

### B. Select a rectangular block and sort it

```python
df_cars = pd.read_csv("quiz_data/features.csv")
df_subset = df_cars.iloc[28:33, 4:7]
df_sorted = df_subset.sort_values("weight", ascending=False)
```

Positions 28,29,30,31,32 require stop 33. The relevant column positions are 4,5,6, so stop 7 gives weight, acceleration, and vehicle_id. The solution uses `4:` because these are the last three columns in this file. Both satisfy the requested slices for this input; `4:7` expresses the intended width explicitly.

The result has five rows and three columns. Sorting returns a new DataFrame and retains original row labels. `ascending=False` puts the heaviest first. Slicing first and sorting second means ranking only these five selected cars, not taking rows 28–32 from a globally weight-sorted table.

**Variation:** `df_cars.sort_values("weight", ascending=False).iloc[:3]` finds the three heaviest cars in the entire table. If the question requests all their features, sort the DataFrame rather than an isolated weight Series.

### C. Convert speed, then calculate kinetic energy

```python
df_speed = pd.read_csv("quiz_data/speed_mass.csv")

def convert_speed(mph):
    return mph * 1609.34 / 3600

mps_series = df_speed["Speed (mph)"].apply(convert_speed)
df_speed["Kinetic Energy (J)"] = (
    0.5 * df_speed["Mass (kg)"] * mps_series**2
)
```

The function accepts one numeric mph value and returns one numeric m/s value. `apply` runs it on each entry and preserves the Series index, so it aligns with the mass column. Pass `convert_speed`, not `convert_speed()`.

Multiplication of Series and squaring happen element by element. Convert units before squaring; using mph directly produces the wrong energy even if no Python error appears. `**2` means square; `^2` is a different operation. The assignment to a named column stores the result; setting only `mps_series.name` would not do that.

**Variation:** if speed doubles while mass stays fixed, kinetic energy quadruples. If mass doubles at fixed speed, energy doubles. These are useful numerical sanity checks for the formula.

### D. Write a mutating Runner.log_run method

Put this inside the supplied Runner class:

```python
def log_run(self, score):
    assert 0 <= score <= 100, "score must be between 0 and 100"
    self.num_runs += 1
    new_stamina = min(self.get_stamina() + score / 10, 100)
    self.set_stamina(new_stamina)
```

`self` is the runner receiving the call. Validate before changing state: an invalid score must not increment the count. `min` caps the updated stamina. Reusing the supplied setter is an explicit requirement and keeps its validation involved.

Ana starts at 50 and logs 80, adding 8 stamina: 58.0 and one run. Ben starts at 95, so the same score produces 100 instead of 103. Scores 0 and 100 are allowed. Score zero still records a run; it just adds no stamina. A negative score or 120 must fail. The method returns None because it changes the existing object rather than creating another Runner.

`ana = ana.log_run(80)` is therefore wrong: it discards the runner reference and assigns None. Calling `other = ana` before logging creates an alias, so the state change is visible through both names.

**Variation:** return self at the end only if a changed problem asks for chaining. A setter's existence does not automatically validate values passed directly to `__init__`; the supplied initializer assigns stamina directly.

### E. TrailRunner inheritance and shared multipliers

```python
class TrailRunner(Runner):
    backpack_multiplier = 1.25

    def set_stamina(self, value):
        assert 0 <= value <= self.backpack_multiplier * 100, "stamina out of range"
        self.stamina = value

    def run_value(self, score):
        return score * self.backpack_multiplier

    @classmethod
    def set_backpack_multiplier(cls, value):
        assert 1 <= value <= 2, "multiplier must be between 1 and 2"
        cls.backpack_multiplier = value
```

The child inherits initialization, getters, string formatting, and pace-buddy linking. It overrides only the behaviors that differ. `run_value` returns a weighted score; it does not mutate stamina. A plain runner scores 60 as 60; a trail runner with multiplier 1.25 scores it as 75.0 and scores 90 as 112.5.

`@classmethod` makes the class available as cls, so setting the multiplier affects existing instances using that shared attribute. No new object is required to see the change. The stamina limit changes from 125 to 150 when the multiplier becomes 1.5; stamina 140 is then valid and 160 invalid. Multiplier .8 or 2.5 must be rejected before assignment.

`cy.backpack_multiplier = 1.8` would create an instance shadow rather than change the whole class. `isinstance(cy, Runner)` is true even though `type(cy) == Runner` is false. Defining no child initializer is appropriate because no extra per-instance state is required.

**Setup nuance:** the imported setup Runner does not contain D's newly written log_run. Do not assume a class imported for E includes methods you added to another earlier class definition. Plain Python import can cache a module, while the solution's `%run` executes its file again. Re-running a class definition also does not upgrade objects already created from the old class.

**Solution pitfall:** the alternate `TrailRunner_alt` refers to `TrailRunner.backpack_multiplier` instead of its own attribute. Changing the alternate class's multiplier can therefore disagree with its calculations. The self/cls version above avoids that cross-class dependency.

**Extension:** changing a class limit does not retroactively repair existing state. If you lower the multiplier after a runner reaches a higher stamina, decide whether a revised problem wants rejection, capping, or another policy; none is requested here.

### F. RunClub composition and a first-compatible-partner search

```python
class RunClub:
    max_gap = 10

    def __init__(self, roster):
        self.runners = []
        for name in roster:
            self.runners.append(Runner(name, roster[name]))

    def swoop_set_buddys(self):
        for runner in self.runners:
            if runner.get_pace_buddy() is not None:
                continue
            for other in self.runners:
                if other is runner:
                    continue
                if other.get_pace_buddy() is not None:
                    continue
                if abs(runner.get_stamina() - other.get_stamina()) <= self.max_gap:
                    runner.set_pace_buddy(other)
                    break
```

The constructor converts the name→stamina dictionary into Runner objects stored on this club instance. The method's outer loop chooses an unpaired runner. The inner loop searches in roster order for the first different, unpaired runner within the allowed gap. Dictionary iteration follows insertion order for this input.

`is` prevents self-pairing by identity. `abs` treats either direction of stamina difference equally. `<=` allows exactly ten. `continue` skips one candidate or already paired runner; `break` stops the inner search immediately after success. Use the supplied setter to link **both** sides. The method returns None; if no candidate fits, that runner remains unpaired.

For Ana 50, Ben 95, Cy 56, Dee 47: Ana takes Cy because Cy is the first compatible available candidate; Ben remains alone; Cy is skipped as already paired; Dee remains alone. Dee is compatible with Ana but cannot reuse Ana.

For the fixed test roster, Kiara 71 pairs with Kai 76; Xavier 83 pairs with Olga 93, including the exact boundary gap; Ines 70 and Hugo 45 remain unpaired. The code must work for other names and generated stamina values, not only this example.

This is a **greedy first-fit rule**, not a search for the closest candidate or a globally best arrangement. Sorting by stamina would change the stated roster-order procedure. Nested loops take up to roughly n² comparisons. An empty or one-person club simply forms no pairs. Repeating the method keeps existing pairs because they are skipped.

**Important distinction from Lecture 15:** the lecture samples pairs then asks both people's preferences. This problem deterministically scans for compatibility. Reuse the object/reference ideas, but implement the actual rule stated here.

### Quiz-style self-checks: try first, then check the answers

1. A threshold split uses `< .4` and `> .4`. What boundary is missing?
2. What shape does `cars.iloc[5:10, 1:4]` have when those positions exist?
3. Why can `.loc[:4]` fail to select the five highest-ranked rows after sorting?
4. Why does `df["speed"].apply(convert_speed())` fail?
5. What does `log_run(0)` do to stamina and run count?
6. Does `run_value(90)` change a trail runner's stamina?
7. What is the difference between `cy.backpack_multiplier = 2` and `TrailRunner.set_backpack_multiplier(2)`?
8. Which object is changed by `buddy = ana; buddy.log_run(10)`?
9. Why is `a.consider(b) and b.consider(a)` a bug in the lecture's session code?
10. Why must the pairing method skip already paired candidates in both loops?
11. If two possible candidates qualify, should F pick the closest one?
12. Does `return self` copy the object?
13. How can a left merge increase the number of rows?
14. With `pd.cut(..., [-np.inf,45,145,np.inf])`, which group contains 145?
15. Why use `game.get("accuracies", {}).get("white")`?
16. What does `df.groupby("team")["points"].mean()` return?
17. Does using the same list for another semester create an independent trial?
18. What do you need to change in a subclass initializer to reuse the parent setup?

#### Answers

1. Exactly .4 is in neither group; use >= on the second side.
2. Five rows and three columns; stopping positions are excluded.
3. Loc uses retained labels, not sorted positions; use iloc[:5].
4. The function is called before apply and has no input; pass the function object.
5. Stamina is unchanged and the run count increases by one.
6. No. It returns a weighted numeric score.
7. The first shadows the value for one object; the second changes the class setting seen by unshadowed objects.
8. Ana: buddy is another name for the same Runner.
9. Short-circuiting can skip B's call, so B fails to record a meeting.
10. To avoid reusing an object and breaking the reciprocal one-partner relationship.
11. No. The requested rule is first compatible in list order.
12. No. It returns the same instance reference.
13. A left key can match several right rows, repeating the left information.
14. The middle interval, (45,145].
15. It handles a missing accuracies key with an empty-dictionary fallback, then returns None if white is absent; a present null needs separate handling.
16. A Series indexed by team labels, with one mean per team.
17. No. The objects retain their state; rebuild or reset all relevant state.
18. Call `super().__init__(...)` with the parent's required arguments before relying on its attributes.

**Preparation limit:** these extensions are plausible ways to vary the taught ideas, not claims about the actual quiz. Assertions are the validation mechanism requested in these exercises; in general Python can disable assert statements under optimization, so do not infer they are unconditional application-level validation.

## Notebook coverage inventory

All 25 notebooks below contain a complete study-summary cell. Starter and solution versions are grouped conceptually in the guide to avoid repeating identical explanations.

- [_assignments/assignment_04/fussy_lab_partner.ipynb](_assignments/assignment_04/fussy_lab_partner.ipynb)
- [_assignments/assignment_05/Assignment5.ipynb](_assignments/assignment_05/Assignment5.ipynb)
- [_assignments/assignment_06/assignment6_mutual_lab_partners.ipynb](_assignments/assignment_06/assignment6_mutual_lab_partners.ipynb)
- [_assignments/assignment_07/Assignment7.ipynb](_assignments/assignment_07/Assignment7.ipynb)
- [_quizzes/practice_quiz_4/practice_quiz_4-solutions.ipynb](_quizzes/practice_quiz_4/practice_quiz_4-solutions.ipynb)
- [_quizzes/practice_quiz_4/practice_quiz_4.ipynb](_quizzes/practice_quiz_4/practice_quiz_4.ipynb)
- [Lab_05/Lab_05-with-solutions.ipynb](Lab_05/Lab_05-with-solutions.ipynb)
- [Lab_05/Lab_05.ipynb](Lab_05/Lab_05.ipynb)
- [Lab_06/Lab_06.ipynb](Lab_06/Lab_06.ipynb)
- [Lab_07/Lab_07.ipynb](Lab_07/Lab_07.ipynb)
- [Lab_08/Lab_08.ipynb](Lab_08/Lab_08.ipynb)
- [Lecture_14_Object_Oriented_Programming/Lecture14-complete.ipynb](Lecture_14_Object_Oriented_Programming/Lecture14-complete.ipynb)
- [Lecture_14_Object_Oriented_Programming/Lecture14.ipynb](Lecture_14_Object_Oriented_Programming/Lecture14.ipynb)
- [Lecture_15_OOP_continued/Lecture15_OOP_with_mutual_selection.ipynb](Lecture_15_OOP_continued/Lecture15_OOP_with_mutual_selection.ipynb)
- [Lecture_15_OOP_continued/Lecture15_OOP_with_mutual_selection_solutions.ipynb](Lecture_15_OOP_continued/Lecture15_OOP_with_mutual_selection_solutions.ipynb)
- [Lecture_16_OOP__Class_Variables_and_Inheritance/Lecture16_OOP_Class_Variables_and_Inheritance.ipynb](Lecture_16_OOP__Class_Variables_and_Inheritance/Lecture16_OOP_Class_Variables_and_Inheritance.ipynb)
- [Lecture_16_OOP__Class_Variables_and_Inheritance/Lecture16_OOP_Class_Variables_and_Inheritance_SOLUTIONS.ipynb](Lecture_16_OOP__Class_Variables_and_Inheritance/Lecture16_OOP_Class_Variables_and_Inheritance_SOLUTIONS.ipynb)
- [Lecture_17_Pandas/lecture_17_loc_and_iloc.ipynb](Lecture_17_Pandas/lecture_17_loc_and_iloc.ipynb)
- [Lecture_17_Pandas/lecture_17_Pandas_basics.ipynb](Lecture_17_Pandas/lecture_17_Pandas_basics.ipynb)
- [Lecture_17_Pandas/lecture_17_solutions.ipynb](Lecture_17_Pandas/lecture_17_solutions.ipynb)
- [lecture_18_Merging_Cleaning_Recoding/lecture_18a_merging.ipynb](lecture_18_Merging_Cleaning_Recoding/lecture_18a_merging.ipynb)
- [lecture_18_Merging_Cleaning_Recoding/lecture_18b_cleaning.ipynb](lecture_18_Merging_Cleaning_Recoding/lecture_18b_cleaning.ipynb)
- [lecture_18_Merging_Cleaning_Recoding/lecture_18c_recoding.ipynb](lecture_18_Merging_Cleaning_Recoding/lecture_18c_recoding.ipynb)
- [Lecture_19_grouping_aggregating/Lecture19.ipynb](Lecture_19_grouping_aggregating/Lecture19.ipynb)
- [Lecture_20_JSON/Lecture20_JSON.ipynb](Lecture_20_JSON/Lecture20_JSON.ipynb)

## Final review checklist

- Explain self, constructor parameters, attributes, and the difference between returning a value and mutating state.
- Trace aliases and reciprocal partner references; check None before following a reference.
- Distinguish instance attributes, class attributes, class methods, and overridden methods.
- Explain why super reuses initialization and why return self enables some chains.
- Predict scalar, Series, DataFrame, and GroupBy outputs at each selection or chain step.
- Write half-open iloc slices and distinguish labels from sorted positions.
- Use query backticks for unusual column names and @ for Python variables.
- Pass a function object to apply and keep measurement units consistent in formulas.
- Build complementary random groups and explain probabilities versus exact group sizes.
- Match tables using the correct key combination and inspect duplicate/unmatched keys.
- Preserve recoverable data while cleaning; test missingness explicitly.
- Classify boundary values correctly with cut and keep unclassified values visible.
- Choose aggregation statistics and row units deliberately, then merge group statistics correctly.
- Navigate dictionary/list nesting, choose load versus loads, and flatten JSON into meaningful rows.
- Rebuild or reset state for independent simulation trials, and distinguish individual outcomes from constant population averages.

## Verification of this guide

- All 25 notebooks retain every original cell, output, execution count, and notebook metadata value, with one study-summary Markdown cell added to each.
- All 113 Python example blocks across those notebook summaries pass syntax checks, and their Markdown code fences are balanced.
- All 63 contents/source/inventory links in this guide resolve to an included section or an existing notebook.
- 27 targeted runtime checks passed using the study-note code and course datasets. These cover Quiz 4 A–F, invalid inputs, pairing boundaries, Lab 7 fitting/prediction/refitting, all three Lab 8 examples, simulation resets, the constant partner-average property, and grade curves.

The original notebooks contain incomplete exercises and deliberate error demonstrations, so this guide does not claim every original cell succeeds in a full run. Quiz extensions do not establish actual exam coverage.


---

<a id="practice-quiz-4-personal-review"></a>

# Review of your saved Practice Quiz 4 attempt

This review checks your saved answers, not just the answer key. Your original code is preserved so you can compare it with the corrections.

| Question | Result | What to remember |
|---|---|---|
| A | Incorrect random column; requested percentages missing | Scalar column assignment broadcasts; request `size=n` for one draw per row |
| B | Correct | Rows 28–32 use 28:33; your 4: slice and descending weight sort are right |
| C | Correct | Lambda plus apply, mph-to-m/s conversion, and energy formula are right |
| D | Correct numeric behavior; missing an explicit requirement | Call set_stamina instead of assigning stamina directly |
| E | Correct | Inheritance, overrides, shared multiplier, class method, and inclusive validation work |
| F | Pairing prevented by a missing method call | Add parentheses to `runner2.get_pace_buddy()` |

The designated assertion failures in D and E are supposed to occur. They are evidence that invalid inputs are rejected, not failed solutions.

## A: generate a vector, not a scalar repeated across the column

Your loop called `np.random.uniform()` once per iteration but assigned each draw to the **whole column**. Because the loop variable x was never used to select a row, every iteration overwrote every player with one shared value. The last draw became everyone's final random_var. Reproducing your code on the course data produced one unique random value and group sizes 18,539 and 0.

Replace the loop and add the requested verification:

```python
df["random_var"] = np.random.uniform(0, 1, size=n)
groupA = df.query("random_var < 0.4")
groupB = df.query("random_var >= 0.4")
print(f"Group A: {100 * len(groupA) / n:.2f}%")
print(f"Group B: {100 * len(groupB) / n:.2f}%")
```

Your query boundaries were already correct; the random column they received was the issue. The n draws supply one value per row. Percentages should be near 40 and 60 rather than exactly equal to them. Inspect `df["random_var"].nunique()` to catch the shared-scalar bug; merely checking that group sizes sum to n would not catch it.

**Reminder added to Lecture 17's main and solution notebooks.**

## B and C: keep your existing answers

B selects a five-by-three block with `df_cars.iloc[28:33,4:]` and sorts weight highest first. Both match the instructions. C correctly passes your lambda to apply, uses the specified conversion constant, and squares the converted speed in `0.5 * mass * speed**2`. These were verified against the actual data.

## D: use the setter the question explicitly requests

Your validation, count increment, stamina increase, and final cap all work numerically. The supplied tests pass, including rejection of score 120. But changing `self.stamina` directly bypasses the required setter.

Use this method inside Runner:

```python
def log_run(self, score):
    assert 0 <= score <= 100, "invalid run score"
    self.num_runs += 1
    new_stamina = min(self.get_stamina() + score / 10, 100)
    self.set_stamina(new_stamina)
```

For Ben at 95 with score 80, calculate 103 locally, cap it to 100, then pass 100 to the setter. Calling the setter with 103 first would fail its validation. Your original `score >= 0 and score <= 100` was correct; rewriting it as a chained comparison is optional. Validate before mutating the count. Score zero counts as a run even though stamina does not increase.

**Reminder added to both Lecture 15 notebooks.**

## E: your subclass is correct

Your `backpack_multiplier`, overridden setter, `run_value`, and class method all work. Existing instances see a class-method multiplier update. Values exactly 1 and 2 are allowed. The error cells reject .8, 2.5, and stamina 160 at multiplier 1.5 as intended. No fix is needed to your E answer.

## F: call the getter before comparing its result with None

Your condition had `runner2.get_pace_buddy is None`. Without parentheses, the expression is a method object, not the stored buddy. It is never None, so that condition prevents every pairing. The resulting all-None output is a logic error that does not raise an exception.

The smallest correction is:

```python
runner2.get_pace_buddy() is None
```

Your own method works on the fixed roster after just that change. A slightly clearer version is:

```python
def swoop_set_buddys(self):
    for runner1 in self.runners:
        for runner2 in self.runners:
            if (runner1 is not runner2
                    and runner1.get_pace_buddy() is None
                    and runner2.get_pace_buddy() is None):
                gap = abs(runner1.get_stamina() - runner2.get_stamina())
                if gap <= self.max_gap:
                    runner1.set_pace_buddy(runner2)
                    break
```

The missing parentheses were the actual bug. Your original `!=` and `RunClub.max_gap` work for this supplied class. Break is useful but was not essential to correctness here: once you pair runner1, your existing unpaired check prevents it being paired again. Adding break makes the first-match stopping rule explicit and saves unnecessary comparisons.

The corrected result is Kiara↔Kai, Xavier↔Olga, and Ines/Hugo unpaired. The exact ten-point gap for Xavier and Olga must qualify. Your constructor and use of the symmetric setter were correct.

**Reminders added to both Lecture 14 notebooks (method versus method result) and both Lecture 16 notebooks (composition and first-fit pairing).**

## Three quick questions to make the corrections stick

1. What is the difference between `df["x"] = np.random.uniform()` and `df["x"] = np.random.uniform(size=len(df))`?
2. Why should the capped stamina be computed before it is passed to set_stamina?
3. Why is `.apply(mph_to_mps)` correct without parentheses, while `get_pace_buddy() is None` needs them?

**Answers:** (1) one shared scalar versus one draw per row; (2) the setter rejects an out-of-range proposal before later capping could happen; (3) apply needs the function to call later, while the None comparison needs the getter's returned value now.

Verification: your original A/D/F behaviors were reproduced, B/C/E were checked, and the minimal corrections were tested separately without changing your saved answer cells.
