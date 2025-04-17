# Spending-Tracker
    /* Mobile optimizations */
    @media (max-width: 640px) {
        .container {
            padding-left: 12px;
            padding-right: 12px;
        }
        
        input[type="date"], 
        input[type="text"], 
        input[type="number"] {
            font-size: 16px; /* Prevents iOS zoom on focus */
            height: 44px; /* Better touch target */
        }
        
        button {
            min-height: 44px; /* Better touch target */
        }
        
        .delete-btn {
            padding: 8px; /* Larger touch area */
        }
    }
</style>

        <form id="expense-form" class="mb-6 sm:mb-8">
            <div class="grid grid-cols-1 gap-3 mb-4">
                <div>
                    <label for="item" class="block text-sm font-medium text-gray-700 mb-1">Item</label>
                    <input type="text" id="item" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
                </div>
                <div>
                    <label for="cost" class="block text-sm font-medium text-gray-700 mb-1">Cost ($)</label>
                    <input type="number" id="cost" required min="0.01" step="0.01" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
                </div>
            </div>
            <div class="flex justify-center">
                <button type="submit" class="bg-teal hover:bg-teal text-white font-medium py-2 px-6 rounded-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-opacity-50 w-full sm:w-auto" style="focus-ring-color: #0A5854;">
                    Add Expense
                </button>
            </div>
        </form>
        
        <div class="mb-6">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-lg sm:text-xl font-semibold text-gray-800">Today's Expenses</h2>
                <div class="text-right">
                    <p class="text-sm text-gray-600">Total Spent Today</p>
                    <p class="text-xl sm:text-2xl font-bold title-teal">$<span id="total-amount">0.00</span></p>
                </div>
            </div>
            
            <div id="expenses-container" class="space-y-3">
                <!-- Expenses will be added here -->
                <p id="no-expenses" class="text-center text-gray-500 py-4">No expenses added yet</p>
            </div>
        </div>
        
        <div class="flex justify-end">
            <button id="clear-all" class="text-sm text-blue-700 hover:text-blue-800 font-medium transition py-2 px-2">
                Clear All Expenses
            </button>
        </div>
    </div>
    
    <div class="bg-white rounded-xl shadow-lg p-4 sm:p-6 mb-4 sm:mb-8">
        <h2 class="text-lg sm:text-xl font-semibold text-gray-800 mb-4">Spending Summary</h2>
        <div class="grid grid-cols-1 sm:grid-cols-3 gap-3 sm:gap-4">
            <div class="bg-green-50 p-4 rounded-lg border border-green-100">
                <p class="text-sm text-gray-600">Today</p>
                <p class="text-lg sm:text-xl font-bold text-green-700">$<span id="today-total">0.00</span></p>
            </div>
            <div class="bg-blue-50 p-4 rounded-lg border border-blue-100">
                <p class="text-sm text-gray-600">This Week</p>
                <p class="text-lg sm:text-xl font-bold text-blue-700">$<span id="week-total">0.00</span></p>
            </div>
            <div class="bg-dark-blue-50 p-4 rounded-lg border border-dark-blue-100">
                <p class="text-sm text-gray-600">This Month</p>
                <p class="text-lg sm:text-xl font-bold text-dark-blue-700">$<span id="month-total">0.00</span></p>
            </div>
        </div>
    </div>
    
    <div class="bg-white rounded-xl shadow-lg p-4 sm:p-6">
        <h2 class="text-lg sm:text-xl font-semibold text-gray-800 mb-4">Custom Date Range</h2>
        <div class="grid grid-cols-1 gap-3 mb-4">
            <div>
                <label for="start-date" class="block text-sm font-medium text-gray-700 mb-1">Start Date</label>
                <input type="date" id="start-date" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
            </div>
            <div>
                <label for="end-date" class="block text-sm font-medium text-gray-700 mb-1">End Date</label>
                <input type="date" id="end-date" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
            </div>
        </div>
        <div class="flex justify-center mb-4 sm:mb-6">
            <button id="view-range" class="bg-teal hover:bg-teal text-white font-medium py-2 px-6 rounded-lg transition duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-opacity-50 w-full sm:w-auto" style="focus-ring-color: #0A5854;">
                View Range
            </button>
        </div>
        <div class="bg-gray-50 p-4 rounded-lg border border-gray-200">
            <div class="flex justify-between items-center">
                <div>
                    <p class="text-sm text-gray-600" id="date-range-label">Select dates above</p>
                    <p class="text-xs text-gray-500 mt-1" id="expense-count">-</p>
                </div>
                <div class="text-right">
                    <p class="text-sm text-gray-600">Total for Range</p>
                    <p class="text-lg sm:text-xl font-bold title-teal">$<span id="range-total">0.00</span></p>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    document.addEventListener('DOMContentLoaded', function() {
        // Initialize from localStorage
        loadExpenses();
        updateSummary();
        
        // Set default dates
        const today = new Date();
        const lastWeek = new Date();
        lastWeek.setDate(today.getDate() - 7);
        
        document.getElementById('start-date').valueAsDate = lastWeek;
        document.getElementById('end-date').valueAsDate = today;
        
        // Form submission
        document.getElementById('expense-form').addEventListener('submit', function(e) {
            e.preventDefault();
            
            const itemInput = document.getElementById('item');
            const costInput = document.getElementById('cost');
            
            const item = itemInput.value.trim();
            const cost = parseFloat(costInput.value);
            
            if (item && !isNaN(cost) && cost > 0) {
                addExpense(item, cost);
                itemInput.value = '';
                costInput.value = '';
                itemInput.focus();
            }
        });
        
        // Clear all expenses
        document.getElementById('clear-all').addEventListener('click', function() {
            if (confirm('Are you sure you want to clear all expenses?')) {
                clearAllExpenses();
            }
        });
        
        // View custom date range
        document.getElementById('view-range').addEventListener('click', function() {
            updateDateRange();
        });
    });
    
    function addExpense(item, cost) {
        const now = new Date();
        const expense = {
            id: Date.now(),
            item: item,
            cost: cost,
            date: now.toISOString()
        };
        
        // Save to localStorage
        const expenses = getExpenses();
        expenses.push(expense);
        localStorage.setItem('expenses', JSON.stringify(expenses));
        
        // Update UI
        displayExpense(expense);
        updateTotal();
        updateSummary();
        
        // Hide "no expenses" message if it's visible
        document.getElementById('no-expenses').style.display = 'none';
    }
    
    function displayExpense(expense) {
        const container = document.getElementById('expenses-container');
        
        const expenseEl = document.createElement('div');
        expenseEl.className = 'expense-card bg-gray-50 p-4 rounded-lg border border-gray-200 flex justify-between items-center fade-in';
        expenseEl.dataset.id = expense.id;
        
        const date = new Date(expense.date);
        const timeStr = date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
        
        expenseEl.innerHTML = `
            <div>
                <h3 class="font-medium text-gray-800">${expense.item}</h3>
                <p class="text-xs text-gray-500">${timeStr}</p>
            </div>
            <div class="flex items-center">
                <span class="font-semibold text-gray-800 mr-3">$${expense.cost.toFixed(2)}</span>
                <button class="delete-btn text-blue-700 hover:text-blue-800 transition p-2">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                        <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z" clip-rule="evenodd" />
                    </svg>
                </button>
            </div>
        `;
        
        // Add delete functionality
        expenseEl.querySelector('.delete-btn').addEventListener('click', function() {
            deleteExpense(expense.id);
        });
        
        container.prepend(expenseEl);
    }
    
    function deleteExpense(id) {
        // Remove from localStorage
        let expenses = getExpenses();
        expenses = expenses.filter(exp => exp.id !== id);
        localStorage.setItem('expenses', JSON.stringify(expenses));
        
        // Remove from UI
        const expenseEl = document.querySelector(`.expense-card[data-id="${id}"]`);
        if (expenseEl) {
            expenseEl.classList.add('opacity-0');
            setTimeout(() => {
                expenseEl.remove();
                updateTotal();
                updateSummary();
                updateDateRange();
                
                // Show "no expenses" message if there are no expenses
                if (expenses.filter(isToday).length === 0) {
                    document.getElementById('no-expenses').style.display = 'block';
                }
            }, 300);
        }
    }
    
    function clearAllExpenses() {
        localStorage.removeItem('expenses');
        document.getElementById('expenses-container').innerHTML = '<p id="no-expenses" class="text-center text-gray-500 py-4">No expenses added yet</p>';
        updateTotal();
        updateSummary();
        updateDateRange();
    }
    
    function loadExpenses() {
        const expenses = getExpenses().filter(isToday);
        
        if (expenses.length > 0) {
            document.getElementById('no-expenses').style.display = 'none';
            expenses.forEach(expense => {
                displayExpense(expense);
            });
            updateTotal();
        }
    }
    
    function updateTotal() {
        const expenses = getExpenses().filter(isToday);
        const total = expenses.reduce((sum, expense) => sum + expense.cost, 0);
        document.getElementById('total-amount').textContent = total.toFixed(2);
    }
    
    function updateSummary() {
        const expenses = getExpenses();
        
        // Today's total
        const todayTotal = expenses.filter(isToday)
            .reduce((sum, expense) => sum + expense.cost, 0);
        document.getElementById('today-total').textContent = todayTotal.toFixed(2);
        
        // This week's total
        const weekTotal = expenses.filter(isThisWeek)
            .reduce((sum, expense) => sum + expense.cost, 0);
        document.getElementById('week-total').textContent = weekTotal.toFixed(2);
        
        // This month's total
        const monthTotal = expenses.filter(isThisMonth)
            .reduce((sum, expense) => sum + expense.cost, 0);
        document.getElementById('month-total').textContent = monthTotal.toFixed(2);
    }
    
    function updateDateRange() {
        const startDateInput = document.getElementById('start-date');
        const endDateInput = document.getElementById('end-date');
        
        if (!startDateInput.value || !endDateInput.value) {
            alert('Please select both start and end dates');
            return;
        }
        
        const startDate = new Date(startDateInput.value);
        startDate.setHours(0, 0, 0, 0);
        
        const endDate = new Date(endDateInput.value);
        endDate.setHours(23, 59, 59, 999);
        
        if (startDate > endDate) {
            alert('Start date must be before end date');
            return;
        }
        
        const expenses = getExpenses().filter(expense => {
            const expenseDate = new Date(expense.date);
            return expenseDate >= startDate && expenseDate <= endDate;
        });
        
        const total = expenses.reduce((sum, expense) => sum + expense.cost, 0);
        
        // Format dates for display
        const startFormatted = startDate.toLocaleDateString('en-US', { month: 'short', day: 'numeric' });
        const endFormatted = endDate.toLocaleDateString('en-US', { month: 'short', day: 'numeric' });
        
        // Update UI
        document.getElementById('date-range-label').textContent = `${startFormatted} - ${endFormatted}`;
        document.getElementById('expense-count').textContent = `${expenses.length} expense${expenses.length !== 1 ? 's' : ''}`;
        document.getElementById('range-total').textContent = total.toFixed(2);
    }
    
    function getExpenses() {
        const expensesJson = localStorage.getItem('expenses');
        return expensesJson ? JSON.parse(expensesJson) : [];
    }
    
    function isToday(expense) {
        const today = new Date();
        const expenseDate = new Date(expense.date);
        return expenseDate.toDateString() === today.toDateString();
    }
    
    function isThisWeek(expense) {
        const now = new Date();
        const expenseDate = new Date(expense.date);
        const weekStart = new Date(now);
        weekStart.setDate(now.getDate() - now.getDay());
        weekStart.setHours(0, 0, 0, 0);
        
        return expenseDate >= weekStart;
    }
    
    function isThisMonth(expense) {
        const now = new Date();
        const expenseDate = new Date(expense.date);
        return expenseDate.getMonth() === now.getMonth() && 
               expenseDate.getFullYear() === now.getFullYear();
    }
</script>
