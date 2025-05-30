# 📦 Command Pattern

**Manba:** [patterns.dev/vanilla/command-pattern](https://www.patterns.dev/vanilla/command-pattern/)

## Kirish

Command Pattern yordamida bajariladigan amallarni ularni chaqiruvchi obyektlardan ajratish mumkin. Bu, ayniqsa, buyruqlarni boshqarish, navbatga qo‘yish yoki keyinchalik bajarish kerak bo‘lganda foydalidir.

## Misol: Onlayn ovqat yetkazib berish platformasi

Foydalanuvchilar buyurtma berish, kuzatish va bekor qilish imkoniyatiga ega.

```javascript
class OrderManager {
  constructor() {
    this.orders = [];
  }

  placeOrder(order, id) {
    this.orders.push(id);
    return `You have successfully ordered ${order} (${id})`;
  }

  trackOrder(id) {
    return `Your order ${id} will arrive in 20 minutes.`;
  }

  cancelOrder(id) {
    this.orders = this.orders.filter(order => order.id !== id);
    return `You have canceled your order ${id}`;
  }
}
```

Ushbu `OrderManager` klassida `placeOrder`, `trackOrder` va `cancelOrder` metodlari mavjud. Ularni to‘g‘ridan-to‘g‘ri chaqirish mumkin:

```javascript
const manager = new OrderManager();

manager.placeOrder("Pad Thai", "1234");
manager.trackOrder("1234");
manager.cancelOrder("1234");
```

Ammo, metodlarni to‘g‘ridan-to‘g‘ri chaqirish ba'zi kamchiliklarga olib kelishi mumkin, masalan, metod nomlarini o‘zgartirish zarurati bo‘lsa, kod bazasida ko‘plab o‘zgarishlar qilish kerak bo‘ladi. Buning o‘rniga, metodlarni ajratib, har bir buyruq uchun alohida funksiyalar yaratish mumkin.

## Refaktorlashtirish: `execute` metodi

`OrderManager` klassini qayta yozamiz: `placeOrder`, `cancelOrder` va `trackOrder` metodlari o‘rniga yagona `execute` metodi bo‘ladi.

```javascript
class OrderManager {
  constructor() {
    this.orders = [];
  }

  execute(command, ...args) {
    return command.execute(this.orders, ...args);
  }
}
```

## Buyruqlarni yaratish

Uchta buyruq yaratamiz:

- `PlaceOrderCommand`
- `CancelOrderCommand`
- `TrackOrderCommand`

```javascript
class Command {
  constructor(execute) {
    this.execute = execute;
  }
}

function PlaceOrderCommand(order, id) {
  return new Command((orders) => {
    orders.push(id);
    return `You have successfully ordered ${order} (${id})`;
  });
}

function CancelOrderCommand(id) {
  return new Command((orders) => {
    orders = orders.filter((order) => order.id !== id);
    return `You have canceled your order ${id}`;
  });
}

function TrackOrderCommand(id) {
  return new Command(() => `Your order ${id} will arrive in 20 minutes.`);
}
```

## Foydalanish

```javascript
const manager = new OrderManager();

manager.execute(new PlaceOrderCommand("Pad Thai", "1234"));
manager.execute(new TrackOrderCommand("1234"));
manager.execute(new CancelOrderCommand("1234"));
```

## Afzalliklari

- Metodlarni chaqiruvchi obyektlardan ajratish imkonini beradi.
- Buyruqlarni boshqarish, navbatga qo‘yish yoki keyinchalik bajarish osonlashadi.

## Kamchiliklari

- Kichik loyihalarda qo‘llash ortiqcha murakkablik keltirib chiqarishi mumkin.

## Manbalar

- [Command Design Pattern - SourceMaking](https://sourcemaking.com/design_patterns/command)
- [Command Pattern - Refactoring Guru](https://refactoring.guru/design-patterns/command)
- [Command Pattern - Carlos Caballero](https://www.carloscaballero.io/command-pattern/)