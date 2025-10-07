```go
package GoVideoStore

import (
  "testing"
)

var customer *Customer

func setup() {
  customer = NewCustomer("Fred")
}

func assertEquals(t *testing.T, expected string, statement string) {
  if statement != expected {
    t.Errorf("\nExpected:\n%s\n\nGot:\n%s", expected, statement)
  }
}

func TestSingleNewReleaseStatement(t *testing.T) {
  setup()
  customer.addRental(NewRental(NewMovie("The Cell", NewRelease), 3))
  assertEquals(t,
    "Rental Record for Fred\n"+
      "\tThe Cell\t9.0\n"+
      "Amount owed is 9.0\n"+
      "You earned 2 frequent renter points",
    customer.statement())
}

func TestDualNewReleaseStatement(t *testing.T) {
  setup()
  customer.addRental(NewRental(NewMovie("The Cell", NewRelease), 3))
  customer.addRental(NewRental(
                       NewMovie("The Tigger Movie", NewRelease), 3))
  assertEquals(t,
    "Rental Record for Fred\n"+
      "\tThe Cell\t9.0\n"+
      "\tThe Tigger Movie\t9.0\n"+
      "Amount owed is 18.0\n"+
      "You earned 4 frequent renter points",
    customer.statement())
}

func TestSingleChildrensStatement(t *testing.T) {
  setup()
  customer.addRental(NewRental(
                       NewMovie("The Tigger Movie", Childrens), 3))
  assertEquals(t,
    "Rental Record for Fred\n"+
      "\tThe Tigger Movie\t1.5\n"+
      "Amount owed is 1.5\n"+
      "You earned 1 frequent renter points",
    customer.statement())
}

func TestMultipleRegularStatement(t *testing.T) {
  setup()
  customer.addRental(NewRental(NewMovie("Plan 9 from Outer Space", Regular),1))
  customer.addRental(NewRental(NewMovie("8 1/2", Regular), 2))
  customer.addRental(NewRental(NewMovie("Eraserhead", Regular), 3))
  assertEquals(t,
    "Rental Record for Fred\n"+
      "\tPlan 9 from Outer Space\t2.0\n"+
      "\t8 1/2\t2.0\n"+
      "\tEraserhead\t3.5\n"+
      "Amount owed is 7.5\n"+
      "You earned 3 frequent renter points",
    customer.statement())
}
```

```go
package GoVideoStore

import "fmt"

type Customer struct {
  name    string
  rentals []*Rental
}

func NewCustomer(name string) *Customer {
  return &Customer{name: name}
}

func (customer *Customer) addRental(rental *Rental) {
  customer.rentals = append(customer.rentals, rental)
}

func (customer *Customer) statement() string {
  totalAmount := 0.0
  frequentRenterPoints := 0
  result := "Rental Record for " + customer.name + "\n"
  for _, rental := range customer.rentals {
    thisAmount := 0.0
    switch rental.movie.movieType {
    case NewRelease:
      thisAmount += float64(rental.daysRented * 3)
    case Regular:
      thisAmount += 2
      if rental.daysRented > 2 {
        thisAmount += float64(rental.daysRented-2) * 1.5
      }
    case Childrens:
      thisAmount += 1.5
      if rental.daysRented > 3 {
        thisAmount += float64(rental.daysRented-3) * 1.5
      }
    }
    frequentRenterPoints++
    if rental.movie.movieType == NewRelease && rental.daysRented > 1 {
      frequentRenterPoints++
    }
    result += fmt.Sprintf("\t%s\t%.1f\n", rental.movie.title, thisAmount)
    totalAmount += thisAmount
  }
  return result +
    fmt.Sprintf(
      "Amount owed is %.1f\nYou earned %d frequent renter points",
      totalAmount, frequentRenterPoints)
}
```

```go
// Rental.go
package GoVideoStore

type Rental struct {
  movie      *Movie
  daysRented int
}

func NewRental(movie *Movie, daysRented int) *Rental {
  return &Rental{movie: movie, daysRented: daysRented}
}
```

```go
// Movie.go
package GoVideoStore

const (
  Regular int = iota
  NewRelease
  Childrens
)

type Movie struct {
  title     string
  movieType int
}

func NewMovie(title string, movieType int) *Movie {
  return &Movie{title: title, movieType: movieType}
}
```

```go
package GoVideoStore

import (
  "testing"
)

var customer *Customer
var newRelease1 *Movie
var newRelease2 *Movie
var childrensMovie *Movie
var regular1 *Movie
var regular2 *Movie
var regular3 *Movie

func setup() {
  customer = NewCustomer("Customer Name")
  newRelease1 = NewMovie("New Release 1", NewRelease)
  newRelease2 = NewMovie("New Release 2", NewRelease)
  childrensMovie = NewMovie("Childrens", Childrens)
  regular1 = NewMovie("Regular 1", Regular)
  regular2 = NewMovie("Regular 2", Regular)
  regular3 = NewMovie("Regular 3", Regular)
}

func assertStringsEqual(t *testing.T, expected string, actual string) {
  if actual != expected {
    t.Errorf("\nExpected:\n%s\n\nGot:\n%s", expected, actual)
  }
}

func assertIntsEqual(t *testing.T, expected int, actual int) {
  if actual != expected {
    t.Errorf("\nExpected:%d Got:%d", expected, actual)
  }
}

func assertFloatsEqual(t *testing.T, expected float64, actual float64) {
  if actual != expected {
    t.Errorf("\nExpected:%f Got:%f", expected, actual)
  }
}

func assertOwedAndPoints(t *testing.T, owed float64, points int) {
  customer.statement()
  assertFloatsEqual(t, owed, customer.totalAmount)
  assertIntsEqual(t, points, customer.frequentRenterPoints)
}

func TestTotalsForOneNewRelease(t *testing.T) {
  setup()
  customer.addRental(NewRental(newRelease1, 3))
  assertOwedAndPoints(t, 9.0, 2)
}

func TestTotalsForTwoNewReleases(t *testing.T) {
  setup()
  customer.addRental(NewRental(newRelease1, 3))
  customer.addRental(NewRental(newRelease2, 3))
  assertOwedAndPoints(t, 18.0, 4)
}

func TestTotalsForOneChildrensMovie(t *testing.T) {
  setup()
  customer.addRental(NewRental(childrensMovie, 3))
  assertOwedAndPoints(t, 1.5, 1)
}

func TestTotalsForManyRegularMovies(t *testing.T) {
  setup()
  customer.addRental(NewRental(regular1, 1))
  customer.addRental(NewRental(regular2, 2))
  customer.addRental(NewRental(regular3, 3))
  assertOwedAndPoints(t, 7.5, 3)
}

func TestStatementFormat(t *testing.T) {
  setup()
  customer.addRental(NewRental(regular1, 1))
  customer.addRental(NewRental(regular2, 2))
  customer.addRental(NewRental(regular3, 3))
  assertStringsEqual(t,
    "Rental Record for Customer Name\n"+
      "\tRegular 1\t2.0\n"+
      "\tRegular 2\t2.0\n"+
      "\tRegular 3\t3.5\n"+
      "Amount owed is 7.5\n"+
      "You earned 3 frequent renter points",
    customer.statement())
}
```

```go
// Customer.go
type Customer struct {
  name                 string
  rentals              []*Rental
  totalAmount          float64
  frequentRenterPoints int
}
```

```go
// Customer.go
package GoVideoStore

import "fmt"

type Customer struct {
  name                 string
  rentals              []*Rental
  totalAmount          float64
  frequentRenterPoints int
}

func NewCustomer(name string) *Customer {
  return &Customer{name: name}
}

func (customer *Customer) addRental(rental *Rental) {
  customer.rentals = append(customer.rentals, rental)
}

func (customer *Customer) makeStatement() string {
  customer.clearTotals()
  return
    customer.makeHeader() +
    customer.makeDetails() +
    customer.makeFooter()
}

func (customer *Customer) clearTotals() {
  customer.totalAmount = 0.0
  customer.frequentRenterPoints = 0
}

func (customer *Customer) makeHeader() string {
  return "Rental Record for " + customer.name + "\n"
}

func (customer *Customer) makeDetails() string {
  rentalDetails := ""
  for _, rental := range customer.rentals {
    rentalDetails += customer.makeDetail(rental)
  }
  return rentalDetails
}

func (customer *Customer) makeDetail(rental *Rental) string {
  amount := customer.determineAmount(rental)
  customer.frequentRenterPoints += customer.determinePoints(rental)
  customer.totalAmount += amount
  return customer.formatDetail(rental, amount)
}

func (customer *Customer) determineAmount(rental *Rental) float64 {
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

func (customer *Customer) determinePoints(rental *Rental) int {
  if rental.movie.movieType == NewRelease && rental.daysRented > 1 {
    return 2
  }
  return 1
}

func (customer *Customer)
formatDetail(rental *Rental, amount float64) string {
  return fmt.Sprintf("\t%s\t%.1f\n", rental.getTitle(), amount)
}

func (customer *Customer) makeFooter() string {
  return fmt.Sprintf("Amount owed is %.1f\n"+
    "You earned %d frequent renter points",
    customer.totalAmount, customer.frequentRenterPoints)
}
```

```go
// Customer.go
func (customer *Customer) makeDetail(rental *Rental) string {
  amount := rental.determineAmount()
  customer.frequentRenterPoints += rental.determinePoints()
  customer.totalAmount += amount
  return customer.formatDetail(rental, amount)
}
```

```go
// Rental.go
Package GoVideoStore

type Rental struct {
  movie      *Movie
  daysRented int
}

func (rental Rental) getTitle() string {
  return rental.movie.title
}

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

func (rental Rental) determinePoints() int {
  if rental.movie.movieType == NewRelease && rental.daysRented > 1 {
    return 2
  }
  return 1
}

func NewRental(movie *Movie, daysRented int) *Rental {
  return &Rental{movie: movie, daysRented: daysRented}
}
```

```go
// RentalStatement.go
type RentalStatement struct {
  name                 string
  rentals              []*Rental
  totalAmount          float64
  frequentRenterPoints int
}
```

```go
// Rental.go
// ...
type Rental struct {
  movie      *Movie
  daysRented int
  rentalType int
}

const (
  Regular int = iota
  NewRelease
  Childrens
)
// ...
func NewRental(movie *Movie, rentalType int, daysRented int) *Rental {
  return &Rental{
    movie:      movie,
    rentalType: rentalType,
    daysRented: daysRented}
}
// ...
```

```go
// RentalType.go
package GoVideoStore

type RentalType interface {
  determineAmount(daysRented int) float64
  determinePoints(daysRented int) int
}

```

```go
// ———Rental.go———
package GoVideoStore

type Rental struct {
  movie      *Movie
  daysRented int
  rentalType RentalType
}

func (rental Rental) getTitle() string {
  return rental.movie.title
}

func (rental Rental) determineAmount() float64 {
  return rental.rentalType.determineAmount(rental.daysRented)
}

func (rental Rental) determinePoints() int {
  return rental.rentalType.determinePoints(rental.daysRented)
}

func NewRental(movie *Movie,
               rentalType RentalType,
               daysRented int) *Rental {
  return &Rental{
    movie:      movie,
    rentalType: rentalType,
    daysRented: daysRented}
}

// ———NewReleaseRental.go———
package GoVideoStore

type NewReleaseRental struct{}

func (rentalType NewReleaseRental)
determineAmount(daysRented int) float64 {
  return float64(daysRented * 3)
}

func (rentalType NewReleaseRental) determinePoints(daysRented int) int {
  if daysRented > 1 {
    return 2
  }
  return 1
}

// ———ChildrensRental.go———
package GoVideoStore

type ChildrensRental struct{}

func (rentalType ChildrensRental)
determineAmount(daysRented int) float64 {
  amount := 1.5
  if daysRented > 3 {
    amount += float64(daysRented-3) * 1.5
  }
  return amount
}

func (rentalType ChildrensRental) determinePoints(daysRented int) int {
  return 1
}

// ———RegularRental.go———
package GoVideoStore

type RegularRental struct{}

func (rentalType RegularRental) determineAmount(daysRented int) float64 {
  amount := 2.0
  if daysRented > 2 {
    amount += float64(daysRented-2) * 1.5
  }
  return amount
}

func (rentalType RegularRental) determinePoints(daysRented int) int {
  return 1
}
```

```go
// ———RentalStatementTest.go———
func TestTotalsForOneNewRelease(t *testing.T) {
  setup()
  statement.addRental(NewRental(newRelease1, NewReleaseRental{}, 3))
  assertOwedAndPoints(t, 9.0, 2)
}
```

```go
// ———Movie.go———
package GoVideoStore

type Movie struct {
  title string
}

func NewMovie(title string) *Movie {
  return &Movie{title: title}
}
```

