# Domain-Driven Design

Domain-driven design (DDD) is an approach to software development that focuses on creating a domain model closely aligned with the business domain. It emphasizes understanding and modeling the problem domain using a common language shared by domain experts and developers.

When applying DDD, the design of the software system revolves around the core domain, which represents the most critical and valuable part of the business. The domain model consists of entities, value objects, aggregates, repositories, services, and other building blocks that capture the essential concepts, rules, and behaviors of the domain.

## 1. Entity Class:

```cs

public class Order
{
    public int Id { get; set; }
    public string CustomerName { get; set; }
    public DateTime OrderDate { get; set; }
    // Other properties and behaviors specific to an order
}

```

## 2. Value Object Class:

```cs

public class Money
{
    public decimal Amount { get; set; }
    public string Currency { get; set; }
    // Methods for currency conversions, arithmetic operations, etc.
}

```

## 3. Aggregate Root Class:

```cs

public class ShoppingCart
{
    public int Id { get; set; }
    public List<OrderItem> Items { get; set; }
    // Methods for adding/removing items, calculating total, etc.
}

```

## 4. Repository Interface:

```cs

public interface IOrderRepository
{
    Order GetById(int orderId);
    void Save(Order order);
    void Delete(Order order);
    // Other repository methods for querying and persisting orders
}

```

## 5. Service Class:

```cs

public class OrderService
{
    private readonly IOrderRepository _orderRepository;

    public OrderService(IOrderRepository orderRepository)
    {
        _orderRepository = orderRepository;
    }

    public void PlaceOrder(Order order)
    {
        // Perform business logic, validations, interactions with other entities
        _orderRepository.Save(order);
    }

}

```

# DDD Part 2

## 1. Entity Class:

```cs
public class Order
{
    public int Id { get; set; }
    public string CustomerName { get; set; }
    public DateTime OrderDate { get; set; }
    public List<OrderItem> Items { get; set; }

        public decimal CalculateTotal()
        {
            decimal total = 0;
            foreach (var item in Items)
            {
                total += item.Quantity * item.UnitPrice;
            }
            return total;
        }

}

```

## 2. Value Object Class:

```cs
public class Money
{
    public decimal Amount { get; set; }
    public string Currency { get; set; }

        public Money(decimal amount, string currency)
        {
            Amount = amount;
            Currency = currency;
        }

        // Methods for currency conversions, arithmetic operations, etc.
        public Money ConvertTo(string targetCurrency)
        {
            // Conversion logic here
            // ...
            return new Money(convertedAmount, targetCurrency);
        }

}

```

## 3. Aggregate Root Class:

```cs

public class ShoppingCart
{
    public int Id { get; set; }
    public List<OrderItem> Items { get; set; }

        public void AddItem(OrderItem item)
        {
            // Check if item already exists and update quantity or add new item
            // ...
        }

        public void RemoveItem(OrderItem item)
        {
            // Remove the specified item from the cart
            // ...
        }

        public decimal CalculateTotal()
        {
            decimal total = 0;
            foreach (var item in Items)
            {
                total += item.Quantity * item.UnitPrice;
            }
            return total;
        }

}

```

## 4. Repository Interface:

```cs

public interface IOrderRepository
{
    Order GetById(int orderId);
    void Save(Order order);
    void Delete(Order order);
    List<Order> GetOrdersByCustomer(string customerName);
    // Other repository methods for querying and persisting orders
}

```

## 5. Service Class:

```cs

public class OrderService
{
    private readonly IOrderRepository _orderRepository;
    private readonly ShoppingCart _shoppingCart;

        public OrderService(IOrderRepository orderRepository, ShoppingCart shoppingCart)
        {
            _orderRepository = orderRepository;
            _shoppingCart = shoppingCart;
        }

        public void AddItemToCart(OrderItem item)
        {
            _shoppingCart.AddItem(item);
        }

        public void RemoveItemFromCart(OrderItem item)
        {
            _shoppingCart.RemoveItem(item);
        }

        public void Checkout(string customerName)
        {
            var order = new Order
            {
                CustomerName = customerName,
                OrderDate = DateTime.Now,
                Items = _shoppingCart.Items
            };

            decimal total = order.CalculateTotal();
            var paymentAmount = new Money(total, "USD");

            // Perform payment processing and other domain-specific logic
            // ...

            _orderRepository.Save(order);
        }

}

```

In the expanded code snippets, we have added additional methods to calculate the total order amount, perform currency conversions, and handle shopping cart operations. The OrderService now interacts with the ShoppingCart and performs the checkout process, including payment processing and persisting the order in the repository.
