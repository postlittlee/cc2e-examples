```go
func (statement *RentalStatement)
makeStatement() string {
  statement.clearTotals()
  return statement.makeHeader() +
         statement.makeDetails() +
         statement.makeFooter()
}

func (statement *RentalStatement)
clearTotals() {
  statement.totalAmount = 0.0
  statement.frequentRenterPoints = 0
}

func (statement *RentalStatement)
makeHeader() string {
  return "Rental Record for " + statement.name + "\n"
}

func (statement *RentalStatement)
makeDetails() string {
  rentalDetails := ""
  for _, rental := range statement.rentals {
    rentalDetails += statement.makeDetail(rental)
  }
  return rentalDetails
}

func (statement *RentalStatement)
makeDetail(rental *Rental) string {
  amount := rental.determineAmount()
  statement.frequentRenterPoints += rental.determinePoints()
  statement.totalAmount += amount
  return statement.formatDetail(rental, amount)
}

func (statement *RentalStatement)
formatDetail(rental *Rental, amount float64) string {
  return fmt.Sprintf("\t%s\t%.1f\n", rental.getTitle(), amount)
}

func (statement *RentalStatement)
makeFooter() string {
  return fmt.Sprintf("Amount owed is %.1f\n"+
    "You earned %d frequent renter points",
    statement.totalAmount, statement.frequentRenterPoints)
}
```
