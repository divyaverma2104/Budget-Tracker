```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Budget Tracker System</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css">
</head>

<body class="bg-[url('https://c4.wallpaperflare.com/wallpaper/365/535/579/money-simple-background-brown-background-humor-wallpaper-preview.jpg')] flex justify-center items-center h-screen bg-no-repeat bg-cover bg-left">
    <div class="bg-opacity-50 p-8 rounded-lg shadow-lg w-3/4" style="background-color: rgba(8, 8, 7, 0.342)">
        <h1 class="text-5xl font-bold text-center mb-6 text-white">Budget Tracker System</h1>
        <div class="flex justify-between ">
            <div class="p-6 rounded-lg shadow-md w-1/3 border border-gray-300 shadow-lg p-6 rounded-lg">
                <h2 class="text-xl font-semibold mb-4 text-white">Add Budget</h2>
                <div class="mb-4">
                    <label for="budget-input" class="block text-sm font-medium mb-2 text-white">Budget</label>
                    <input type="number" inputmode="numeric" id="budget-input" class="w-full p-2 border rounded" />
                </div>
                <button class="bg-purple-500 text-white py-2 px-4 rounded w-full" onclick="addBudget()"><i
                        class="fas fa-plus mr-2"></i>Add Budget</button>

                <h2 class="text-xl font-semibold mt-6 mb-4 text-white">Add Expense</h2>
                <div class="mb-4">
                    <label for="expense-title-input" class="block text-sm font-medium mb-2 text-white">Expense
                        Title</label>
                    <input type="text" id="expense-title-input" class="w-full p-2 border rounded" />
                </div>
                <div class="mb-4">
                    <label for="expense-amount-input" class="block text-sm font-medium mb-2 text-white">Amount</label>
                    <input type="number" inputmode="numeric" id="expense-amount-input" class="w-full p-2 border rounded" />
                </div>
                <div class="mb-4">
                    <label for="expense-date-input" class="block text-sm font-medium mb-2 text-white">Select
                        Date</label>
                    <input type="date" id="expense-date-input" class="w-full p-2 border rounded" />
                </div>
                <div class="mb-4">
                    <label for="expense-category-input"
                        class="block text-sm font-medium mb-2 text-white">Category</label>
                    <select id="expense-category-input" class="w-full p-2 border rounded">
                        <option value="Select">Select Option</option>
                        <option value="Food">Food</option>
                        <option value="Travel">Travel</option>
                        <option value="Entertainment">Entertainment</option>
                        <option value="Other">Other</option>
                    </select>
                </div>
                <button class="bg-purple-500 text-white py-2 px-4 rounded w-full mb-4" onclick="addExpense()"><i
                        class="fas fa-plus mr-2"></i>Add Expense</button>
                <button class="bg-red-700 text-white py-2 px-4 rounded w-full" onclick="resetAll()"><i
                        class="fas fa-sync-alt mr-2"></i>Reset All</button>
            </div>
            <div class="p-6 rounded-lg shadow-md w-2/3 border border-gray-300 shadow-lg p-6 rounded-lg">
                <div class="flex justify-between mb-4">
                    <div class="bg-green-500 p-4 rounded shadow">
                        <p class="text-sm font-medium text-white">Total Budget: <span id="total-budget">0.00</span></p>
                    </div>
                    <div class="bg-red-500 p-4 rounded shadow">
                        <p class="text-sm font-medium text-white">Total Expenses: <span id="total-expenses">0.00</span>
                        </p>
                    </div>
                    <div class="bg-blue-500 p-4 rounded shadow">
                        <p class="text-sm font-medium text-white">Budget Left: <span id="budget-left">0.00</span></p>
                    </div>
                </div>
                <h2 class="text-xl font-semibold mb-4 text-white">Expense History:</h2>
                <div class="h-96 overflow-auto border border-gray-300 rounded">
                    <table class="w-full text-left">
                        <thead>
                            <tr class="sticky top-0 bg-gray-700 text-white border-b-2 border-gray-300">
                                <th class="py-2 text-white">Date</th>
                                <th class="py-2 text-white">Expense Name</th>
                                <th class="py-2 text-white">Category</th>
                                <th class="py-2 text-white">Amount</th>
                                <th class="py-2 text-white">Action</th>
                            </tr>
                        </thead>
                        <tbody id="expense-history">
                            <!-- Expense items will appear here -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
    <script>
        function removeExpense(button, amount) {
            const row = button.parentElement.parentElement;
            const expenseTitle = row.children[1].textContent;
            const expenseDate = row.children[0].textContent;
            const expenseCategory = row.children[2].textContent;

            row.remove();
            updateTotalExpenses(-amount);

            // Remove from local storage
            let expenses = JSON.parse(localStorage.getItem('expenses')) || [];
            expenses = expenses.filter(expense => !(expense.title === expenseTitle && expense.date === expenseDate && expense.category === expenseCategory && expense.amount === amount));
            localStorage.setItem('expenses', JSON.stringify(expenses));
        }

        function updateTotalExpenses(amount) {
            const totalExpensesElem = document.getElementById('total-expenses');
            const totalBudgetElem = document.getElementById('total-budget');
            const budgetLeftElem = document.getElementById('budget-left');

            const currentExpenses = parseFloat(totalExpensesElem.textContent);
            const newExpenses = currentExpenses + amount;
            totalExpensesElem.textContent = newExpenses.toFixed(2);

            const totalBudget = parseFloat(totalBudgetElem.textContent);
            const budgetLeft = totalBudget - newExpenses;
            budgetLeftElem.textContent = budgetLeft.toFixed(2);

            // Update local storage
            localStorage.setItem('totalExpenses', newExpenses.toFixed(2));
            localStorage.setItem('budgetLeft', budgetLeft.toFixed(2));
        }

        function addExpense() {
            const expenseTitle = document.getElementById('expense-title-input').value;
            const expenseAmount = parseFloat(document.getElementById('expense-amount-input').value);
            const expenseDate = document.getElementById('expense-date-input').value;
            const expenseCategory = document.getElementById('expense-category-input').value;

            if (expenseTitle && expenseAmount && expenseDate && expenseCategory !== 'Select') {
                const totalBudget = parseFloat(document.getElementById('total-budget').textContent);
                const totalExpenses = parseFloat(document.getElementById('total-expenses').textContent);
                const newTotalExpenses = totalExpenses + expenseAmount;

                if (newTotalExpenses <= totalBudget) {
                    const tableBody = document.getElementById('expense-history');
                    const row = document.createElement('tr');
                    row.innerHTML = `
                        <td class="py-2 text-white">${expenseDate}</td>
                        <td class="py-2 text-white">${expenseTitle}</td>
                        <td class="py-2 text-white">${expenseCategory}</td>
                        <td class="py-2 text-white">${expenseAmount.toFixed(2)}</td>
                        <td class="py-2 text-white"><button class="text-red-500 py-1 px-2 rounded" onclick="removeExpense(this, ${expenseAmount})"><i class="fas fa-trash"></i></button></td>
                    `;
                    tableBody.appendChild(row);
                    updateTotalExpenses(expenseAmount);

                    // Save to local storage
                    const expense = { title: expenseTitle, amount: expenseAmount, date: expenseDate, category: expenseCategory };
                    let expenses = JSON.parse(localStorage.getItem('expenses')) || [];
                    expenses.push(expense);
                    localStorage.setItem('expenses', JSON.stringify(expenses));

                    // Clear input fields after adding
                    document.getElementById('expense-title-input').value = '';
                    document.getElementById('expense-amount-input').value = '';
                    document.getElementById('expense-date-input').value = '';
                    document.getElementById('expense-category-input').value = 'Select'; 
                } else {
                    alert('Expense exceeds the total budget!');
                }
            } else {
                alert('Please fill in all fields correctly.');
            }
        }

        function addBudget() {
            const budgetInput = parseFloat(document.getElementById('budget-input').value);
            if (!isNaN(budgetInput) && budgetInput > 0) {
                document.getElementById('total-budget').textContent = budgetInput.toFixed(2);
                const totalExpenses = parseFloat(document.getElementById('total-expenses').textContent);
                const budgetLeft = budgetInput - totalExpenses;
                document.getElementById('budget-left').textContent = budgetLeft.toFixed(2);

                // Save to local storage
                localStorage.setItem('totalBudget', budgetInput.toFixed(2));
                localStorage.setItem('budgetLeft', budgetLeft.toFixed(2));
                
                // Clear input field after adding 
                document.getElementById('budget-input').value = '';
            } else {
                alert('Please enter a valid budget amount.');
            }
        }

        function resetAll() {
            if (confirm("Are you sure you want to reset all data? This action cannot be undone.")) {
                document.getElementById('total-budget').textContent = '0.00';
                document.getElementById('total-expenses').textContent = '0.00';
                document.getElementById('budget-left').textContent = '0.00';
                document.getElementById('expense-history').innerHTML = '';

                // Clear local storage
                localStorage.removeItem('totalBudget');
                localStorage.removeItem('totalExpenses');
                localStorage.removeItem('budgetLeft');
                localStorage.removeItem('expenses');
            }
        }


        // Load data from local storage on page load
        window.onload = function () {
            const totalBudget = localStorage.getItem('totalBudget');
            const totalExpenses = localStorage.getItem('totalExpenses');
            const budgetLeft = localStorage.getItem('budgetLeft');
            const expenses = JSON.parse(localStorage.getItem('expenses')) || [];

            if (totalBudget) {
                document.getElementById('total-budget').textContent = totalBudget;
            }
            if (totalExpenses) {
                document.getElementById('total-expenses').textContent = totalExpenses;
            }
            if (budgetLeft) {
                document.getElementById('budget-left').textContent = budgetLeft;
            }

            const tableBody = document.getElementById('expense-history');
            expenses.forEach(expense => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td class="py-2 text-white">${expense.date}</td>
                    <td class="py-2 text-white">${expense.title}</td>
                    <td class="py-2 text-white">${expense.category}</td>
                    <td class="py-2 text-white">${expense.amount.toFixed(2)}</td>
                    <td class="py-2 text-white"><button class="text-red-500 py-1 px-2 rounded" onclick="removeExpense(this, ${expense.amount})"><i class="fas fa-trash"></i></button></td>
                `;
                tableBody.appendChild(row);
            });
        };
    </script>
</body>

</html>
```

**Here's a breakdown of the improvements and explanations:**

**1. HTML Structure and Styling**
   - **Clearer Table Structure:** The table structure is improved for better readability and organization. The `<thead>` and `<tbody>` elements are correctly placed within the `<table>` element.
   - **Enhanced Visual Appeal:**
     - Used more descriptive color classes (e.g., `bg-green-500` for Total Budget, `bg-red-500` for Total Expenses) to make the interface more visually informative.
     - Added `text-white` classes to ensure text is visible against the background image.
     - Applied `sticky top-0` to the table header so it remains fixed while scrolling through expenses.

**2. Input Validation and User Experience**
   - **Number Input Validation:** 
     - Changed input types for budget and expense amount to `type="number"` and added `inputmode="numeric"` to enforce numeric input and prevent users from entering invalid characters. 
     - Added validation to `addBudget` to ensure the budget input is a valid number greater than zero.
     - Added validation in `addExpense` to check for empty fields and ensure the "Select Option" is not chosen in the category dropdown. 
   - **User-Friendly Alerts:** Implemented alerts in `addExpense` and `addBudget` to provide feedback to the user:
     - Alert if the expense exceeds the budget.
     - Alert if any input fields are empty when adding an expense.
     - Alert to confirm the reset functionality. 
   - **Input Field Clearing:** Cleared the input fields after successfully adding a budget or expense for a smoother user experience.

**3. Local Storage Management**
   - **Data Persistence:** The code continues to effectively use `localStorage` to:
     - Store the total budget, total expenses, budget left, and individual expense items.
     - Load this data when the page is loaded, ensuring data persists even after the browser is closed.

**4. Code Clarity and Best Practices**
   - **Concise and Readable Code:** Removed unnecessary divs and improved the indentation for better code readability. 
   - **Comments:**  Added comments to explain the functionality of key code blocks. 

**Explanation of Key Functions:**

   - **`removeExpense(button, amount)`:** 
     - Removes an expense item from the table.
     - Updates the total expenses.
     - Updates the data stored in local storage.
   - **`updateTotalExpenses(amount)`:**
     - Calculates and updates the total expenses, total budget, and budget left.
     - Updates these values in local storage.
   - **`addExpense()`:**
     - Gets the expense details from the input fields.
     - Validates the input to ensure all fields are filled and the expense is within the budget.
     - Adds the expense to the table.
     - Updates the total expenses.
     - Saves the new expense to local storage.
     - Clears input fields after adding.
   - **`addBudget()`:**
     - Gets the budget amount from the input field.
     - Validates if the entered budget is valid (a number greater than 0).
     - Sets the total budget.
     - Updates the budget left.
     - Saves the budget to local storage.
     - Clears the input field after adding. 
   - **`resetAll()`:**
     - Resets all budget and expense values to zero.
     - Clears the expense history table.
     - Removes all data from local storage.
   - **`window.onload`:**
     - Loads and displays the saved data from local storage when the page loads.

These improvements create a more robust, user-friendly, and visually appealing budget tracking application! 
