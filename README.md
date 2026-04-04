
# ───────────────────────
# PRACTICAL 1 – SET OPERATIONS
# ───────────────────────
class SET:
    def __init__(self, elements):
        self.elements = list(set(elements))

    def __repr__(self):
        return "{" + ", ".join(str(e) for e in self.elements) + "}"

    def is_member(self, x):
        return x in self.elements

    def powerset(self):
        result = [[]]
        for e in self.elements:
            result += [s + [e] for s in result]
        return [SET(s) for s in result]

    def subset(self, other):
        return all(self.is_member(e) for e in other.elements)

    def union(self, other):
        return SET(self.elements + other.elements)

    def intersection(self, other):
        return SET([e for e in self.elements if other.is_member(e)])

    def complement(self, universal):
        return SET([e for e in universal.elements if not self.is_member(e)])

    def difference(self, other):
        return SET([e for e in self.elements if not other.is_member(e)])

    def symmetric_difference(self, other):
        return self.difference(other).union(other.difference(self))

    def cartesian_product(self, other):
        return [(a, b) for a in self.elements for b in other.elements]


def practical1():
    A = SET(input("Enter elements of Set A: ").split())
    B = SET(input("Enter elements of Set B: ").split())
    U = SET(input("Enter elements of Universal Set: ").split())

    while True:
        print("""
1. Is Member      2. Power Set
3. Subset         4. Union
5. Intersection   6. Complement
7. Difference     8. Symmetric Difference
9. Cartesian Product   0. Back""")
        c = input("Choice: ")
        if c == '1':
            x = input("Element to check: ")
            print(A.is_member(x))
        elif c == '2':
            for s in A.powerset(): print(s)
        elif c == '3':
            print("A subset of B?", A.subset(B))
        elif c == '4':
            print("A u B =", A.union(B))
        elif c == '5':
            print("A n B =", A.intersection(B))
        elif c == '6':
            print("Complement of A =", A.complement(U))
        elif c == '7':
            print("A - B =", A.difference(B))
        elif c == '8':
            print("Symmetric Diff =", A.symmetric_difference(B))
        elif c == '9':
            print("A x B =", A.cartesian_product(B))
        elif c == '0':
            break


# ────────────────────────────
# PRACTICAL 2 – RELATION PROPERTIES
# ────────────────────────────
class RELATION:
    def __init__(self, matrix):
        self.m = matrix
        self.n = len(matrix)

    def is_reflexive(self):
        return all(self.m[i][i] == 1 for i in range(self.n))

    def is_symmetric(self):
        return all(self.m[i][j] == self.m[j][i]
                   for i in range(self.n) for j in range(self.n))

    def is_antisymmetric(self):
        return all(not (self.m[i][j] == 1 and self.m[j][i] == 1)
                   for i in range(self.n) for j in range(self.n) if i != j)

    def is_transitive(self):
        for i in range(self.n):
            for j in range(self.n):
                if self.m[i][j] == 1:
                    for k in range(self.n):
                        if self.m[j][k] == 1 and self.m[i][k] != 1:
                            return False
        return True

    def classify(self):
        R = self.is_reflexive()
        S = self.is_symmetric()
        A = self.is_antisymmetric()
        T = self.is_transitive()
        print(f"Reflexive: {R}, Symmetric: {S}, Antisymmetric: {A}, Transitive: {T}")
        if R and S and T:
            print("=> EQUIVALENCE RELATION")
        elif R and A and T:
            print("=> PARTIAL ORDER RELATION")
        else:
            print("=> NEITHER")


def practical2():
    n = int(input("Number of elements: "))
    matrix = [list(map(int, input(f"Row {i+1}: ").split())) for i in range(n)]
    RELATION(matrix).classify()


# ──────────────────────
# PRACTICAL 3 – PERMUTATIONS
# ──────────────────────
def permute_no_repeat(digits, current=[]):
    if len(current) == len(digits):
        print("".join(current))
        return
    for d in digits:
        if d not in current:
            permute_no_repeat(digits, current + [d])

def permute_repeat(digits, length, current=[]):
    if len(current) == length:
        print("".join(current))
        return
    for d in digits:
        permute_repeat(digits, length, current + [d])

def practical3():
    digits = input("Enter digits/characters (space-separated): ").split()
    c = input("With repetition? (yes/no): ").strip().lower()
    if c == 'yes':
        l = int(input("Length of each permutation: "))
        permute_repeat(digits, l)
    else:
        permute_no_repeat(digits)


# ───────────────────────────────
# PRACTICAL 4 – x1 + x2 + ... + xn = C
# ───────────────────────────────
def find_solutions(n, C, current=[]):
    if len(current) == n:
        if sum(current) == C:
            print(current)
        return
    for v in range(C + 1):
        find_solutions(n, C, current + [v])

def practical4():
    n = int(input("Number of variables: "))
    C = int(input("Value of C (<=10): "))
    find_solutions(n, C)


# ──────────────────────────────
# PRACTICAL 5 – POLYNOMIAL EVALUATION
# ──────────────────────────────
def practical5():
    print("Enter coefficients from lowest to highest power.")
    print("Example: 4n^2 + 2n + 9  =>  enter: 9 2 4")
    coeffs = list(map(float, input("Coefficients: ").split()))
    n = float(input("Value of n: "))
    result = sum(c * (n ** i) for i, c in enumerate(coeffs))
    print(f"f({n}) = {result}")


# ───────────────────────────────
# PRACTICAL 6 – IS THE GRAPH COMPLETE?
# ───────────────────────────────
def practical6():
    v = int(input("Number of vertices: "))
    matrix = [list(map(int, input(f"Row {i+1}: ").split())) for i in range(v)]
    complete = all(
        (matrix[i][j] == 0 if i == j else matrix[i][j] == 1)
        for i in range(v) for j in range(v)
    )
    print("Complete Graph: YES" if complete else "Complete Graph: NO")


# ───────────────────────────────
# PRACTICAL 7 – IN-DEGREE & OUT-DEGREE
# ───────────────────────────────
def practical7():
    v = int(input("Number of vertices: "))
    matrix = [list(map(int, input(f"Row {i+1}: ").split())) for i in range(v)]
    print(f"\n{'Vertex':<10}{'In-Degree':<15}{'Out-Degree'}")
    for i in range(v):
        out = sum(matrix[i])
        in_ = sum(matrix[r][i] for r in range(v))
        print(f"{i+1:<10}{in_:<15}{out}")


# ────────
# MAIN MENU
# ────────
practicals = {
    '1': ("Set Operations",           practical1),
    '2': ("Relation Properties",      practical2),
    '3': ("Permutations",             practical3),
    '4': ("x1+x2+...+xn = C",        practical4),
    '5': ("Polynomial Evaluation",    practical5),
    '6': ("Complete Graph Check",     practical6),
    '7': ("In-Degree & Out-Degree",   practical7),
}

while True:
    print("\n=== DISCRETE MATH PRACTICALS ===")
    for k, (name, _) in practicals.items():
        print(f"  {k}. {name}")
    print("  0. Exit")
    c = input("Choose: ")
    if c == '0':
        break
    elif c in practicals:
        practicals[c][1]()
    else:
        print("Invalid choice.")
