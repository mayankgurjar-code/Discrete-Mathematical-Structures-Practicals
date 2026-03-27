# Discrete-Mathematical-Structures-Practicals


# ============================================================
#   Discrete Mathematical Structures - All Practicals
#   B.Sc. (H) Computer Science - Semester II (NEP UGCF 2022)
# ============================================================


# ============================================================
# PRACTICAL 1 - SET CLASS WITH ALL SET OPERATIONS
# ============================================================

class SET:
    """
    A class to represent a Mathematical Set and perform set operations.
    Internally stores elements in a Python list (no duplicates).
    """

    def __init__(self, elements):
        # Remove duplicates by converting to a regular list manually
        self.elements = []
        for e in elements:
            if e not in self.elements:
                self.elements.append(e)

    def __repr__(self):
        return "{" + ", ".join(str(e) for e in self.elements) + "}"

    # a. Check if an element is a member of the set
    def is_member(self, element):
        return element in self.elements  # returns True or False

    # b. Power Set - all possible subsets of the set
    def powerset(self):
        result = [[]]  # start with empty set
        for elem in self.elements:
            # For each existing subset, create a new subset that includes current elem
            result += [subset + [elem] for subset in result]
        return [SET(s) for s in result]

    # c. Check if current set is a subset of another set
    def subset(self, other):
        for e in self.elements:
            if not other.is_member(e):
                return False
        return True

    # d. Union and Intersection
    def union(self, other):
        combined = self.elements[:]
        for e in other.elements:
            if e not in combined:
                combined.append(e)
        return SET(combined)

    def intersection(self, other):
        common = [e for e in self.elements if other.is_member(e)]
        return SET(common)

    # e. Complement (Universal Set - Current Set)
    def complement(self, universal):
        result = [e for e in universal.elements if not self.is_member(e)]
        return SET(result)

    # f. Set Difference (A - B) and Symmetric Difference (A △ B)
    def difference(self, other):
        result = [e for e in self.elements if not other.is_member(e)]
        return SET(result)

    def symmetric_difference(self, other):
        # Elements in A or B but NOT in both
        a_minus_b = self.difference(other)
        b_minus_a = other.difference(self)
        return a_minus_b.union(b_minus_a)

    # g. Cartesian Product A × B
    def cartesian_product(self, other):
        result = []
        for a in self.elements:
            for b in other.elements:
                result.append((a, b))
        return result


def practical1():
    print("\n" + "="*60)
    print("        PRACTICAL 1 - SET OPERATIONS")
    print("="*60)

    # Taking input from user
    a_input = input("Enter elements of Set A (space-separated): ").split()
    b_input = input("Enter elements of Set B (space-separated): ").split()
    u_input = input("Enter elements of Universal Set (space-separated): ").split()

    A = SET(a_input)
    B = SET(b_input)
    U = SET(u_input)

    print(f"\nSet A = {A}")
    print(f"Set B = {B}")
    print(f"Universal Set = {U}")

    while True:
        print("""
--- SET OPERATIONS MENU ---
1. Is Member
2. Power Set of A
3. Is A a Subset of B?
4. Union (A ∪ B)
5. Intersection (A ∩ B)
6. Complement of A
7. Set Difference (A - B)
8. Symmetric Difference (A △ B)
9. Cartesian Product (A × B)
0. Exit Practical 1
        """)
        choice = input("Enter your choice: ")

        if choice == '1':
            elem = input("Enter element to check: ")
            print(f"Is '{elem}' a member of A? {A.is_member(elem)}")
        elif choice == '2':
            ps = A.powerset()
            print(f"Power Set of A (total {len(ps)} subsets):")
            for s in ps:
                print(" ", s)
        elif choice == '3':
            print(f"Is A a subset of B? {A.subset(B)}")
        elif choice == '4':
            print(f"A ∪ B = {A.union(B)}")
        elif choice == '5':
            print(f"A ∩ B = {A.intersection(B)}")
        elif choice == '6':
            print(f"Complement of A = {A.complement(U)}")
        elif choice == '7':
            print(f"A - B = {A.difference(B)}")
        elif choice == '8':
            print(f"A △ B = {A.symmetric_difference(B)}")
        elif choice == '9':
            cp = A.cartesian_product(B)
            print(f"A × B = {{ {', '.join(str(p) for p in cp)} }}")
        elif choice == '0':
            break
        else:
            print("Invalid choice. Try again.")


# ============================================================
# PRACTICAL 2 - RELATION CLASS (Matrix Representation)
# ============================================================

class RELATION:
    """
    Represents a binary relation using an n×n matrix.
    matrix[i][j] = 1 means element i is related to element j.
    """

    def __init__(self, matrix):
        self.matrix = matrix
        self.n = len(matrix)  # number of elements

    def is_reflexive(self):
        # Every element must be related to itself → diagonal must be all 1s
        for i in range(self.n):
            if self.matrix[i][i] != 1:
                return False
        return True

    def is_symmetric(self):
        # If (i,j) is in relation, then (j,i) must also be in relation
        for i in range(self.n):
            for j in range(self.n):
                if self.matrix[i][j] == 1 and self.matrix[j][i] != 1:
                    return False
        return True

    def is_antisymmetric(self):
        # If (i,j) and (j,i) both exist, then i must equal j
        for i in range(self.n):
            for j in range(self.n):
                if i != j and self.matrix[i][j] == 1 and self.matrix[j][i] == 1:
                    return False
        return True

    def is_transitive(self):
        # If (i,j) and (j,k) exist, then (i,k) must also exist
        for i in range(self.n):
            for j in range(self.n):
                if self.matrix[i][j] == 1:
                    for k in range(self.n):
                        if self.matrix[j][k] == 1 and self.matrix[i][k] != 1:
                            return False
        return True

    def classify(self):
        r = self.is_reflexive()
        s = self.is_symmetric()
        a = self.is_antisymmetric()
        t = self.is_transitive()

        print(f"\n  Reflexive:     {r}")
        print(f"  Symmetric:     {s}")
        print(f"  Antisymmetric: {a}")
        print(f"  Transitive:    {t}")

        # Equivalence = Reflexive + Symmetric + Transitive
        if r and s and t:
            print("\n  ✅ The relation is an EQUIVALENCE RELATION.")
        # Partial Order = Reflexive + Antisymmetric + Transitive
        elif r and a and t:
            print("\n  ✅ The relation is a PARTIAL ORDER RELATION.")
        else:
            print("\n  ❌ The relation is NEITHER Equivalence NOR Partial Order.")


def practical2():
    print("\n" + "="*60)
    print("        PRACTICAL 2 - RELATION PROPERTIES")
    print("="*60)

    n = int(input("Enter number of elements in the set: "))
    print(f"Enter the {n}×{n} relation matrix row by row (space-separated 0s and 1s):")

    matrix = []
    for i in range(n):
        row = list(map(int, input(f"Row {i+1}: ").split()))
        matrix.append(row)

    print("\nMatrix entered:")
    for row in matrix:
        print(" ", row)

    rel = RELATION(matrix)
    rel.classify()


# ============================================================
# PRACTICAL 3 - PERMUTATIONS (with and without repetition)
# ============================================================

def permutations_without_repetition(digits, current=[]):
    """Generate all permutations WITHOUT repetition using recursion (backtracking)."""
    if len(current) == len(digits):
        print("".join(str(x) for x in current))
        return
    for d in digits:
        if d not in current:  # don't reuse a digit
            current.append(d)
            permutations_without_repetition(digits, current)
            current.pop()  # backtrack


def permutations_with_repetition(digits, length, current=[]):
    """Generate all permutations WITH repetition of given length."""
    if len(current) == length:
        print("".join(str(x) for x in current))
        return
    for d in digits:
        current.append(d)
        permutations_with_repetition(digits, length, current)
        current.pop()  # backtrack


def practical3():
    print("\n" + "="*60)
    print("        PRACTICAL 3 - PERMUTATIONS")
    print("="*60)

    digits = input("Enter digits/characters (space-separated): ").split()
    choice = input("With repetition? (yes/no): ").strip().lower()

    if choice == 'yes':
        length = int(input(f"Enter length of each permutation (max {len(digits)}): "))
        print(f"\nAll permutations WITH repetition of length {length}:")
        permutations_with_repetition(digits, length)
    else:
        print(f"\nAll permutations WITHOUT repetition:")
        permutations_without_repetition(digits)


# ============================================================
# PRACTICAL 4 - SOLUTIONS TO x1 + x2 + ... + xn = C
# ============================================================

def find_solutions(n, C, index=0, current=[], solutions=[]):
    """
    Brute force: Try all combinations of non-negative integers
    for x1, x2, ..., xn such that their sum equals C.
    """
    if index == n:
        # All n variables assigned; check if sum equals C
        if sum(current) == C:
            solutions.append(current[:])
        return solutions

    for val in range(C + 1):  # each variable can be 0 to C
        current.append(val)
        find_solutions(n, C, index + 1, current, solutions)
        current.pop()

    return solutions


def practical4():
    print("\n" + "="*60)
    print("        PRACTICAL 4 - SOLUTIONS TO x1+x2+...+xn = C")
    print("="*60)

    n = int(input("Enter number of variables (n): "))
    C = int(input("Enter constant C (C <= 10): "))

    if C > 10:
        print("C must be <= 10 as per the problem.")
        return

    solutions = find_solutions(n, C, 0, [], [])

    print(f"\nTotal solutions found: {len(solutions)}")
    for i, sol in enumerate(solutions, 1):
        equation = " + ".join(f"x{j+1}={v}" for j, v in enumerate(sol))
        print(f"  Solution {i}: {equation}  → Sum = {sum(sol)}")


# ============================================================
# PRACTICAL 5 - POLYNOMIAL EVALUATION
# ============================================================

def evaluate_polynomial(coefficients, n):
    """
    Evaluate a polynomial for a given value n.
    coefficients[i] represents the coefficient of n^i.
    Example: f(n) = 4n^2 + 2n + 9 → coefficients = [9, 2, 4]
                                       (index 0=constant, 1=n, 2=n^2)
    """
    result = 0
    for power, coeff in enumerate(coefficients):
        result += coeff * (n ** power)
    return result


def practical5():
    print("\n" + "="*60)
    print("        PRACTICAL 5 - POLYNOMIAL EVALUATION")
    print("="*60)
    print("Enter coefficients from LOWEST to HIGHEST power.")
    print("Example: For f(n) = 4n^2 + 2n + 9, enter: 9 2 4")

    coefficients = list(map(float, input("Enter coefficients: ").split()))
    n = float(input("Enter value of n: "))

    result = evaluate_polynomial(coefficients, n)

    # Display the polynomial nicely
    terms = []
    for power, coeff in enumerate(coefficients):
        if coeff == 0:
            continue
        if power == 0:
            terms.append(str(int(coeff) if coeff == int(coeff) else coeff))
        elif power == 1:
            terms.append(f"{int(coeff) if coeff == int(coeff) else coeff}n")
        else:
            terms.append(f"{int(coeff) if coeff == int(coeff) else coeff}n^{power}")

    polynomial_str = " + ".join(reversed(terms))
    print(f"\n  f(n) = {polynomial_str}")
    print(f"  f({int(n) if n == int(n) else n}) = {result}")


# ============================================================
# PRACTICAL 6 - CHECK IF GRAPH IS COMPLETE
# ============================================================

def practical6():
    """
    A graph is COMPLETE if every pair of distinct vertices is connected.
    In the adjacency matrix:
      - All diagonal elements = 0 (no self-loops)
      - All non-diagonal elements = 1 (every vertex connects to every other)
    """
    print("\n" + "="*60)
    print("        PRACTICAL 6 - COMPLETE GRAPH CHECK")
    print("="*60)

    v = int(input("Enter number of vertices: "))
    print(f"Enter the {v}×{v} adjacency matrix row by row (space-separated 0s and 1s):")
    print("(0 = no edge, 1 = edge exists; diagonal should be 0)")

    matrix = []
    for i in range(v):
        row = list(map(int, input(f"Row {i+1}: ").split()))
        matrix.append(row)

    is_complete = True
    for i in range(v):
        for j in range(v):
            if i == j:
                # Diagonal must be 0 (no self loops)
                if matrix[i][j] != 0:
                    is_complete = False
                    break
            else:
                # All off-diagonal must be 1
                if matrix[i][j] != 1:
                    is_complete = False
                    break

    print(f"\nAdjacency Matrix:")
    for row in matrix:
        print(" ", row)

    if is_complete:
        print(f"\n  ✅ The graph IS a Complete Graph (K{v}).")
        print(f"     Every vertex is connected to every other vertex.")
    else:
        print(f"\n  ❌ The graph is NOT a Complete Graph.")
        print(f"     Some vertices are missing edges.")


# ============================================================
# PRACTICAL 7 - IN-DEGREE AND OUT-DEGREE OF DIRECTED GRAPH
# ============================================================

def practical7():
    """
    For a DIRECTED graph represented as adjacency matrix:
    - Out-degree of vertex i = sum of row i (edges going OUT from i)
    - In-degree of vertex i  = sum of column i (edges coming IN to i)
    """
    print("\n" + "="*60)
    print("        PRACTICAL 7 - IN-DEGREE AND OUT-DEGREE")
    print("="*60)

    v = int(input("Enter number of vertices: "))
    print(f"Enter the {v}×{v} adjacency matrix for the DIRECTED graph:")
    print("(matrix[i][j] = 1 means there is a directed edge from vertex i to vertex j)")

    matrix = []
    for i in range(v):
        row = list(map(int, input(f"Row {i+1}: ").split()))
        matrix.append(row)

    print(f"\nAdjacency Matrix:")
    for row in matrix:
        print(" ", row)

    print(f"\n{'Vertex':<10} {'In-Degree':<15} {'Out-Degree':<15}")
    print("-" * 40)

    for i in range(v):
        out_degree = sum(matrix[i])           # sum of row i
        in_degree  = sum(matrix[r][i] for r in range(v))  # sum of column i
        print(f"  {i+1:<8} {in_degree:<15} {out_degree:<15}")

    # Total degrees
    total_in  = sum(sum(matrix[r][i] for r in range(v)) for i in range(v))
    total_out = sum(sum(row) for row in matrix)
    print("-" * 40)
    print(f"  {'Total':<8} {total_in:<15} {total_out:<15}")
    print(f"\n  Note: Total In-Degree always equals Total Out-Degree = Total Edges = {total_out}")


# ============================================================
# MAIN MENU - Run any practical
# ============================================================

def main():
    print("\n" + "="*60)
    print("   DISCRETE MATHEMATICAL STRUCTURES - PRACTICALS")
    print("   B.Sc. (H) Computer Science, Semester II")
    print("="*60)

    menu = {
        '1': ("SET Operations",                    practical1),
        '2': ("RELATION Properties & Classification", practical2),
        '3': ("PERMUTATIONS (with/without repetition)", practical3),
        '4': ("Solutions to x1+x2+...+xn = C",    practical4),
        '5': ("POLYNOMIAL Evaluation",             practical5),
        '6': ("COMPLETE GRAPH Check",              practical6),
        '7': ("IN-DEGREE & OUT-DEGREE of Digraph", practical7),
    }

    while True:
        print("\n--- MAIN MENU ---")
        for key, (name, _) in menu.items():
            print(f"  {key}. Practical {key}: {name}")
        print("  0. Exit")

        choice = input("\nSelect a practical to run: ").strip()

        if choice == '0':
            print("\nGoodbye! All the best for your practicals! 🎓")
            break
        elif choice in menu:
            menu[choice][1]()  # call the function
        else:
            print("Invalid choice. Please enter 1-7 or 0 to exit.")


if __name__ == "__main__":
    main()
