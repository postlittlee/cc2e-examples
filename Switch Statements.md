```go
func (rental Rental) determineAmount() float64 {
  amount := 0.0
  switch rental.movie.movieType {
  case NewRelease:
    amount += float64(rental.daysRented * 3)
  case Regular:
    amount += 2
    if rental.daysRented > 2 {
      amount += float64(rental.daysRented-2) * 1.5
  }
  case Childrens:
    amount += 1.5
    if rental.daysRented > 3 {
      amount += float64(rental.daysRented-3) * 1.5
    }
  }
  return amount
}
```

```go
type RentalTypeFactory interface {
  make(typeName string) RentalType
}

type RentalTypeFactoryImpl struct{}

func (r RentalTypeFactoryImpl) make(typeName string) RentalType {
  switch typeName {
  case "childrens":
    return ChildrensRental{}
  case "new release":
    return NewReleaseRental{}
  case "regular":
    return RegularRental{}
  }
  return nil
}
```
