Operator penugasan adalah operator yang digabungkan sekaligus kedalam variabel untuk menghasilkan nilai yang sama seperti nilai yang dideklarasikannya.

---

| Operator Aritmatika | Augmrnted Assigment |
| ------------------- | ------------------- |
| a = a + 10          | a += 10             |
| a = a - 10          | a -= 10             |
| a = a \* 10         | a \*= 10            |
| a = a / 10          | a /= 10             |
| a = a ~/ 10         | a ~/= 10            |
| a = a %/ 10         | a %= 10             |

#

**Claasic**

```Dart
String a = a + 10;
```

**Modern**

```dart
String a += 10;
```

# Increment

```dart
var a += 10;

++a;
print(a);

```

#### Kode Saya

```dart
void main() {
  print('\n' * 5);

  var a = 10;
  var b = 20;
  var c = 10;
  var d = 2.0;
  var e = 10;
  var f = 20;
  var g = 0;
  var h = 0;
  var i = 0;
  var j = 0;

  a += 5;
  b -= 10;
  c *= 5;
  d /= 2.0;
  e %= 3;
  f ~/= 2;

  'Increment';
  ++i;
  --j;

  'Decrement';
  g++;
  h--;

  print(' $a =>  a += 5 '
      ' $b => b -= 10 '
      ' $c => c *= 5 '
      ' $d => d /= 2.0 '
      ' $e => e %= 3 '
      ' $f => f ~/= 2 '
      ' $g => g++ Decrement'
      ' $h => h-- '
      ' $i => ++i Increment'
      ' $j => --j ');

  print('\n' * 5);
}

```

> Dokumentasi resmi tentang [tentang operator](https://dart.dev/guides/language/language-tour#assignment-operators "dart.dev")
