## ❌ Without SRP

```php
class OrderService
{
    public function placeOrder(array $orderData)
    {
        // 1. Validate data
        $this->validate($orderData);

        // 2. Create order in DB
        $order = Order::create($orderData);

        // 3. Send confirmation email
        Mail::to($order->user->email)->send(new OrderConfirmationMail($order));

        // 4. Notify warehouse
        Http::post('http://warehouse.local/api/notify', ['order_id' => $order->id]);

        // 5. Log order creation
        Log::info("Order placed: " . $order->id);

        return $order;
    }

    private function validate(array $data)
    {
        // Validation logic
        if (empty($data['product_id'])) {
            throw new \Exception("Product ID is required");
        }
    }
}
```

## ✅ With SRP

```php
class OrderService
{
    public function __construct(
        private OrderValidator $validator,
        private OrderRepository $repository,
        private OrderEmailSender $emailSender,
        private WarehouseNotifier $notifier,
        private OrderLogger $logger,
    ) {}

    public function placeOrder(array $orderData)
    {
        $this->validator->validate($orderData);
        $order = $this->repository->create($orderData);
        $this->emailSender->send($order);
        $this->notifier->notify($order);
        $this->logger->logCreation($order);

        return $order;
    }
}
```

### `OrderValidator.php`

```php
class OrderValidator
{
    public function validate(array $data)
    {
        if (empty($data['product_id'])) {
            throw new \Exception("Product ID is required");
        }
    }
}
```

### `OrderRepository.php`

```php
class OrderRepository
{
    public function create(array $data): Order
    {
        return Order::create($data);
    }
}
```

### `OrderEmailSender.php`

```php
class OrderEmailSender
{
    public function send(Order $order)
    {
        Mail::to($order->user->email)->send(new OrderConfirmationMail($order));
    }
}
```

### `WarehouseNotifier.php`

```php
class WarehouseNotifier
{
    public function notify(Order $order)
    {
        Http::post('http://warehouse.local/api/notify', ['order_id' => $order->id]);
    }
}
```

### `OrderLogger.php`

```php
class OrderLogger
{
    public function logCreation(Order $order)
    {
        Log::info("Order placed: " . $order->id);
    }
}
```