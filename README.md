The `monomorphic` library
==========================

What is this?
-------------
This library provides the type-class and functions to convert polymorphic data-types to/from its monomorphic representation types.
It is convenient to provide a monomorphic interface for dependently-typed programs.

Usage
-----
Consider the following example:

```haskell
data Nat = Z | S Nat
data SNat (n :: Nat) where
  SZ :: SNat Z
  SS :: SNat n -> SNat (S n)

instance Monomorphicable SNat where
  type MonomorphicRep SNat = Int
  promote 0     = Monomorphic SZ
  promote n
    | n < 0     = error "negative number"
    | otherwise =
        case promote (n - 1) of
          Monomorphic sn -> Monomorphic (SS sn)
  demote (Monomorphic sn) = toInt sn

data Vector (a :: *) (n :: Nat) where
  VNil :: Vector a Z
  (:-) :: a -> Vector a n -> Vector a (S n)

instance Monomorphicable (Vector a) whre
  type MonomorphicRep (Vector a) = [a]
  demote  (Monomorphic n) = toList n
  promote [] = Monomorphic Nil
  promote (x:xs) =
    case promote xs of
      Monomorphic xs' -> Monomorphic $ x :- xs'
```

`Monomorphic k`{.haskell} is the wrapper type to eliminate the polymorphic part of the types.

