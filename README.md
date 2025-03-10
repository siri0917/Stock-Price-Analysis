# Stock Price Analysis
class StockPriceAnalysis:
    def init(self):
        self.stock_prices = []  # Array to store stock prices over time
        self.price_history = PriceHistory()  # Linked List to store price history
        self.price_stack = PriceStack()  # Stack for next greater price
        self.price_queue = PriceQueue()  # Queue for moving average calculation

    def add_stock_price(self, price):
        self.stock_prices.append(price)
        self.price_history.add_price(price)  # Add price to history
        self.price_queue.enqueue(price)  # Add price to moving average queue
        self.price_stack.update_stack(price)  # Update stack for next greater price

    def get_next_greater_price(self, index):
        """ Get next greater price after the given index using the stack. """
        if index < len(self.stock_prices):
            return self.price_stack.get_next_greater(index)
        return None

    def calculate_moving_average(self):
        """ Calculate moving average from the queue. """
        return self.price_queue.calculate_moving_average()

    def show_price_history(self):
        """ Display the history of stock prices. """
        self.price_history.display_history()


class PriceStack:
    def init(self):
        self.stack = []
        self.next_greater = {}

    def update_stack(self, price):
        """ Updates the stack with new price and calculates the next greater element. """
        while self.stack and self.stack[-1][0] < price:
            prev_price, index = self.stack.pop()
            self.next_greater[index] = price  # Mark the next greater price
        self.stack.append((price, len(self.next_greater)))  # Add new price and its index

    def get_next_greater(self, index):
        """ Returns the next greater price for the given index. """
        return self.next_greater.get(index, None)


class PriceQueue:
    def init(self, window_size=5):
        self.queue = []
        self.window_size = window_size

    def enqueue(self, price):
        """ Adds a new stock price to the queue and removes the oldest if window exceeds size. """
        self.queue.append(price)
        if len(self.queue) > self.window_size:
            self.queue.pop(0)  # Remove the oldest price

    def calculate_moving_average(self):
        """ Calculate the moving average of the prices in the queue. """
        if not self.queue:
            return 0
        return sum(self.queue) / len(self.queue)


class PriceHistory:
    def init(self):
        self.head = None

    def add_price(self, price):
        """ Adds a new stock price to the linked list. """
        new_node = PriceNode(price)
        if not self.head:
            self.head = new_node
        else:
            temp = self.head
            while temp.next:
                temp = temp.next
            temp.next = new_node

    def display_history(self):
        """ Display all stock prices stored in history (linked list). """
        if not self.head:
            print("No price history available.")
            return
        print("\n Price History:")
        temp = self.head
        while temp:
            print(f"₹{temp.price}")
            temp = temp.next


class PriceNode:
    def init(self, price):
        self.price = price
        self.next = None


# Main program execution
stock_system = StockPriceAnalysis()

while True:
    print("\n Welcome to the Stock Price Analysis System")
    print("1. Add Stock Price")
    print("2. View Next Greater Price for a Day")
    print("3. View Moving Average of Last 5 Prices")
    print("4. View Price History")
    print("5. Exit")

    choice = input("Enter your choice: ")

    if choice == "1":
        price = float(input("Enter stock price: ₹"))
        stock_system.add_stock_price(price)
        print(f"Stock Price ₹{price} added.")
    elif choice == "2":
        index = int(input("Enter the index of the day: "))
        next_price = stock_system.get_next_greater_price(index)
        if next_price:
            print(f" The next greater price after day {index} is ₹{next_price}.")
        else:
            print(" No greater price found in the future.")
    elif choice == "3":
        moving_avg = stock_system.calculate_moving_average()
        print(f" Moving Average of last 5 stock prices: ₹{moving_avg}")
    elif choice == "4":
        stock_system.show_price_history()
    elif choice == "5":
        print(" Exiting... Thank you for using the Stock Price Analysis System!")
        break
    else:
        print(" Invalid choice! Please try again.")
