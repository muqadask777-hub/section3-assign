

# c1.js code

// ============================================================
// C1 — E-Commerce Product Manager: Shopping Cart System
// ============================================================

// ---- ORIGINAL JUNIOR DEVELOPER CODE (with bug explanations) ----

var cartA = { owner: 'Asad', items: [{ name: 'Laptop', price: 150000 }], total: 150000 };

// BUG 1: cartB = cartA is NOT a copy — both variables point to the SAME object in memory.
// Any change to cartB will also change cartA because they share the same reference.
var cartB = cartA;

// Tab 2 user adds an item
cartB.items.push({ name: 'Mouse', price: 2500 });
cartB.total = cartB.total + 2500;

// TASK 1: Predicted outputs (with explanations)
console.log('Tab 1 cart items:', cartA.items.length);
// Output: 2
// WHY: cartB = cartA does not copy — both point to the same object.
// Pushing to cartB.items also pushes to cartA.items.

console.log('Tab 1 total:', cartA.total);
// Output: 152500
// WHY: Same reference issue. Changing cartB.total changes cartA.total too.

function applyPromo(cart, discount) {
  // BUG 2: This function mutates the original object directly.
  // Because objects are passed by reference (the reference value is passed),
  // changing cart.total here changes the original object outside the function.
  cart.total = cart.total - discount;
  cart.promoApplied = true;
  return cart;
}

const originalCart = { owner: 'Sara', items: ['Book'], total: 500 };
const discountedCart = applyPromo(originalCart, 50);

console.log('Original total:', originalCart.total);
// Output: 450
// WHY: applyPromo mutated originalCart directly. The original is changed — this is a bug.


// ============================================================
// FIXED VERSION
// ============================================================

// Task 3: Fixed code using const/let, deep copy, and pure functions

// Deep copy cartA so cartB is fully independent (including nested items array)
const cartAFixed = { owner: 'Asad', items: [{ name: 'Laptop', price: 150000 }], total: 150000 };
const cartBFixed = structuredClone(cartAFixed); // deep copy — nested objects/arrays are also copied

// Tab 2 adds an item — only affects cartBFixed
cartBFixed.items.push({ name: 'Mouse', price: 2500 });
cartBFixed.total = cartBFixed.total + 2500;

console.log('\n--- After Fix ---');
console.log('Tab 1 items:', cartAFixed.items.length); // 1 — unchanged
console.log('Tab 1 total:', cartAFixed.total);        // 150000 — unchanged
console.log('Tab 2 items:', cartBFixed.items.length); // 2
console.log('Tab 2 total:', cartBFixed.total);        // 152500

// Fixed applyPromo — does NOT mutate the original, returns a new cart object
function applyPromoFixed(cart, discount) {
  return {
    ...cart,
    total: cart.total - discount,
    promoApplied: true,
  };
}

const originalCartFixed = { owner: 'Sara', items: ['Book'], total: 500 };
const discountedCartFixed = applyPromoFixed(originalCartFixed, 50);

console.log('\nOriginal total after promo:', originalCartFixed.total); // 500 — unchanged
console.log('Discounted cart total:', discountedCartFixed.total);      // 450


# // Task 4: addItem function — pure, returns new cart, proves original is unchanged

function addItem(cart, item) {
  return {
    ...cart,
    items: [...cart.items, item],           // new array with item added
    total: cart.total + item.price,
  };
}

const myCart = { owner: 'Bilal', items: [{ name: 'Keyboard', price: 3000 }], total: 3000 };

console.log('\n--- addItem Test ---');
console.log('Before addItem — items:', myCart.items.length, '| total:', myCart.total);

const updatedCart = addItem(myCart, { name: 'Monitor', price: 25000 });

console.log('After addItem  — original items:', myCart.items.length, '| original total:', myCart.total); // unchanged
console.log('New cart items:', updatedCart.items.length, '| new total:', updatedCart.total);


**output**

<img width="372" height="297" alt="Screenshot 2026-06-15 234228" src="https://github.com/user-attachments/assets/23f5626c-b718-4b9d-9ef4-1d65e2f926c0" />




# c2.js code


// ============================================================
// C2 — User Registration System: Validation Engine
// ============================================================

function validateUser(data) {
  const errors = [];

  // Work on a local copy — pure function, do NOT mutate original data
  const name     = data.name;
  const email    = data.email;
  const password = data.password;
  const role     = data.role;

  // --- name: must be a non-empty string ---
  if (typeof name !== 'string' || name.trim() === '') {
    errors.push('Name cannot be empty');
  }

  // --- email: must be a string containing '@' and '.' ---
  if (typeof email !== 'string' || !email.includes('@') || !email.includes('.')) {
    errors.push('Invalid email format');
  }

  // --- age: coerce to number, validate range 13–120 ---
  const ageCoerced = Number(data.age);
  if (isNaN(ageCoerced)) {
    errors.push('Age must be a valid number');
  } else if (ageCoerced < 13 || ageCoerced > 120) {
    errors.push('Age must be between 13 and 120');
  }

  // --- password: must be string, minimum 8 characters ---
  if (typeof password !== 'string' || password.length < 8) {
    errors.push('Password must be at least 8 characters');
  }

  // --- role: if provided must be valid, else default to 'user' ---
  const validRoles = ['admin', 'editor', 'user'];
  const assignedRole = role ?? 'user'; // default to 'user' if not provided
  if (typeof role !== 'undefined' && !validRoles.includes(role)) {
    errors.push(`Role must be one of: ${validRoles.join(', ')}`);
  }

  // --- Return result ---
  if (errors.length > 0) {
    return { valid: false, errors };
  }

  return {
    valid: true,
    user: {
      name:     name.trim(),
      email:    email,
      age:      ageCoerced,       // coerced and cleaned
      password: password,
      role:     assignedRole,
    },
  };
}

// ============================================================
// Test Cases 
// ============================================================

// Test 1: age comes as string '25' — should be coerced, role defaults to 'user'
console.log('Test 1:', JSON.stringify(
  validateUser({ name: 'Ali', email: 'ali@test.com', age: '25', password: 'pass1234' }),
  null, 2
));

// Test 2: empty name, bad email, age 10 (too young), short password
console.log('Test 2:', JSON.stringify(
  validateUser({ name: '', email: 'notanemail', age: 10, password: 'abc' }),
  null, 2
));

// Test 3: valid but role = 'adm' (invalid role)
console.log('Test 3:', JSON.stringify(
  validateUser({ name: 'Sara', email: 'sara@x.io', age: 30, password: 'secure99', role: 'adm' }),
  null, 2
));

// Test 4: age = '17abc' — not coercible to a valid number
console.log('Test 4:', JSON.stringify(
  validateUser({ name: 'X', email: 'x@x.com', age: '17abc', password: 'hello123' }),
  null, 2
));

// Bonus Test 5: valid admin user
console.log('Test 5 (admin):', JSON.stringify(
  validateUser({ name: 'Sara', email: 'sara@x.io', age: 30, password: 'secure99', role: 'admin' }),
  null, 2
));

**output**

<img width="308" height="376" alt="Screenshot 2026-06-15 235017" src="https://github.com/user-attachments/assets/7b639ca8-597e-4117-ac45-78fb295bc793" />




<img width="265" height="235" alt="Screenshot 2026-06-15 235213" src="https://github.com/user-attachments/assets/941f6005-9140-4c3c-8129-1e5e0b5dce93" />



# c3.js code


// ============================================================
// C3 — Student Grade Management System: Report Generator
// ============================================================

// Given data — DO NOT MODIFY
const students = [
  { name: 'Asad',   scores: [85, 90, 78, 92],       present: true  },
  { name: 'Sara',   scores: [70, 65, '80', 75],      present: true  },
  { name: 'Ali',    scores: [55, 60, 50, null],      present: false },
  { name: 'Fatima', scores: [95, 98, 100, 92],       present: true  },
  { name: 'Umar',   scores: [],                      present: true  },
];


// ============================================================
// 1. getAverage(scores)
//    - Coerce strings to numbers using Number()
//    - Skip nulls and non-coercible values (NaN)
//    - Return 0 if no valid scores
//    - Round to 1 decimal place
// ============================================================
function getAverage(scores) {
  const validScores = scores
    .map(s => Number(s))              // coerce strings; null becomes 0 via Number(null)=0
    .filter(s => s !== null && !isNaN(s) && typeof s === 'number');
    // Note: Number(null) = 0, which would be wrong for skipping null
    // Better approach: filter nulls BEFORE coercing

  // Correct approach: filter out nulls first, then coerce and check NaN
  const cleaned = scores
    .filter(s => s !== null && s !== undefined)     // skip null/undefined
    .map(s => Number(s))                             // coerce (e.g. '80' → 80)
    .filter(s => !isNaN(s));                         // skip non-coercible like NaN

  if (cleaned.length === 0) return 0;

  const sum = cleaned.reduce((acc, val) => acc + val, 0);
  return Math.round((sum / cleaned.length) * 10) / 10; // round to 1 decimal
}


// ============================================================
// 2. getGrade(average)
//    Pure function — returns letter grade based on average
// ============================================================
function getGrade(average) {
  if (average >= 90) return 'A+';
  if (average >= 80) return 'A';
  if (average >= 70) return 'B';
  if (average >= 60) return 'C';
  if (average >= 50) return 'D';
  return 'F';
}


// ============================================================
// 3. generateReport(students)
//    - Returns a NEW array of report objects
//    - Does NOT mutate the original students array
// ============================================================
function generateReport(students) {
  return students.map(student => {
    const average = getAverage(student.scores);
    const grade   = getGrade(average);
    const status  = student.present ? 'present' : 'absent';
    const passed  = average >= 60 && student.present === true;

    // Return a new object — student object is never modified
    return {
      name:    student.name,
      average: average,
      grade:   grade,
      status:  status,
      passed:  passed,
    };
  });
}


// ============================================================
// 4. getSummary(report)
//    - Returns { total, passed, failed, topStudent, classAverage }
// ============================================================
function getSummary(report) {
  const total   = report.length;
  const passed  = report.filter(r => r.passed).length;
  const failed  = total - passed;

  // topStudent: student with highest average
  const topStudent = report.reduce((top, current) =>
    current.average > top.average ? current : top
  ).name;

  // classAverage: average of all student averages
  const sumOfAverages = report.reduce((acc, r) => acc + r.average, 0);
  const classAverage  = Math.round((sumOfAverages / total) * 10) / 10;

  return { total, passed, failed, topStudent, classAverage };
}


// ============================================================
// Run & Display
// ============================================================

// Prove students array is unchanged BEFORE generateReport
console.log('--- students BEFORE generateReport ---');
console.log(JSON.stringify(students, null, 2));

const report = generateReport(students);

// Prove students array is unchanged AFTER generateReport
console.log('\n--- students AFTER generateReport (must be identical) ---');
console.log(JSON.stringify(students, null, 2));

// Print full report
console.log('\n--- Full Report ---');
report.forEach(r => {
  console.log(
    `${r.name.padEnd(7)}: avg=${r.average.toFixed(1)}, grade='${r.grade}', status='${r.status}', passed=${r.passed}`
  );
});

// Print summary
const summary = getSummary(report);
console.log('\n--- Summary ---');
console.log(summary);


// ============================================================
// Expected Output:
// Asad   : avg=86.3, grade='A',  status='present', passed=true
// Sara   : avg=72.5, grade='B',  status='present', passed=true   ← '80' coerced
// Ali    : avg=55.0, grade='D',  status='absent',  passed=false  ← absent + null skipped
// Fatima : avg=96.3, grade='A+', status='present', passed=true
// Umar   : avg=0,   grade='F',  status='present', passed=false  ← empty scores
//
// Summary: { total:5, passed:3, failed:2, topStudent:'Fatima', classAverage:62.0 }
// ============================================================

**output**
<img width="329" height="394" alt="Screenshot 2026-06-15 233254" src="https://github.com/user-attachments/assets/f80757f1-6e43-4602-a75b-5be3778d857e" />

<img width="372" height="380" alt="Screenshot 2026-06-15 233327" src="https://github.com/user-attachments/assets/7b821fa0-fe51-405a-b5d1-7feee099c035" />

<img width="235" height="376" alt="Screenshot 2026-06-15 233402" src="https://github.com/user-attachments/assets/9fb5afb4-9cae-4dc1-97d9-0e7bf4008969" />

<img width="405" height="369" alt="Screenshot 2026-06-15 233510" src="https://github.com/user-attachments/assets/3a7f63dc-618a-43c0-873a-1593246f28e5" />

<img width="266" height="162" alt="Screenshot 2026-06-15 233538" src="https://github.com/user-attachments/assets/a30fb357-d9cd-48db-9515-d7c1421aef0c" />


