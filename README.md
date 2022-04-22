# Custom-Calendar


```swift

// MARK: - MODELS, DateValue
struct DateValue: Identifiable {
    var id: String = UUID().uuidString
    var day: Int
    var date: Date
}

struct CustomDatePicker: View {
    
    @Binding var currentDate: Date
    
    // Month update on arrow button click
    @State private var currentMonth: Int = 0
    
    // Name of Day
    private let days: [String] = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"]
    
    // Days
    private let columns: [GridItem] = Array(repeating: GridItem(.flexible()), count: 7)
    
    var body: some View {
        
        VStack(spacing: 35) {
            // MARK: - HEADER
            HStack(spacing: 20) {
                VStack(alignment: .leading, spacing: 10) {
                    Text(extractDate()[0])
                        .font(.caption)
                        .fontWeight(.semibold)
                    Text(extractDate()[1])
                        .font(.title.bold())
                } //: VSTACK
                Spacer(minLength: 0)
                
                Button {
                    withAnimation {
                        currentMonth -= 1
                    }
                } label: {
                    Image(systemName: "chevron.left")
                        .font(.title2)
                }
                
                Button {
                    currentMonth += 1
                } label: {
                    Image(systemName: "chevron.right")
                        .font(.title2)
                }
                
            } //: HSTACK - HEADER
            .padding(.horizontal)
            
            // MARK: - DAY VIEW
            HStack(spacing: 0) {
                ForEach(days, id: \.self) { day in
                    Text(day)
                        .font(.callout)
                        .fontWeight(.semibold)
                        .frame(maxWidth: .infinity)
                }
            } //: HSTACK - DAY VIEW
            

            // MARK: - DATES
            LazyVGrid(columns: columns, spacing: 15) {
                ForEach(extractDate()) { value in
                    CardView(value: value)
                }
            } //: LAZYVGRID DATES
            
        } //: VSTACK
        .onChange(of: currentMonth) { newValue in
            // updating Month...
            currentDate = getCurrentMonth()
        }
        
    }
    
    // MARK: CHANGE DAY DESIGN IF YO UWANT
    @ViewBuilder
    private func CardView(value: DateValue) -> some View {
        VStack {
            if value.day != -1 {
                Text("\(value.day)")
                    .font(.title3.bold())
            }
        }
        .padding(.vertical, 8)
        .frame(height: 60, alignment: .top)
    }
    
    // extracting Year And Month for display...
    private func extractDate() -> [String] {
        let formatter: DateFormatter = DateFormatter()
        formatter.dateFormat = "YYYY MMMM"
        let date: String = formatter.string(from: currentDate)
        return date.components(separatedBy: " ")
    }
    
    // with `currentMonth`, extract relevant dates
    private func extractDate() -> [DateValue] {
        let calendar: Calendar = Calendar.current
        // Getting Current Month Data...
        let currentMonth: Date = getCurrentMonth()
        var days: [DateValue] = currentMonth.getAllDates().compactMap { date -> DateValue in
            // getting days...
            let day: Int = calendar.component(.day, from: date)
            return DateValue(day: day, date: date)
        }
        // MARK: YOU CAN FIX OFFSET DAYS HERE
        // adding offset days to get exact week day...
        let firstWeekDay = calendar.component(.weekday, from: days.first!.date)
        for _ in 0..<firstWeekDay - 1 {
            days.insert(DateValue(day: -1, date: Date()), at: 0)
        }
        return days
    }
    
    // get current date of month with `currentMonth`
    private func getCurrentMonth() -> Date {
        let calendar: Calendar = Calendar.current
        // Getting Current Month Data...
        guard let currentMonth = calendar.date(byAdding: .month, value: self.currentMonth, to: Date()) else {
            return Date()
        }
        return currentMonth
    }
}

struct CustomDatePicker_Previews: PreviewProvider {
    static var previews: some View {
        Home(date: .init())
    }
}

// Extending Date to get Current Month Dates...
extension Date {
    
    func getAllDates() -> [Date] {
        let calendar: Calendar = Calendar.current
        
        // getting start Date...
        let dateComponents: DateComponents = calendar.dateComponents([.year, .month], from: self)
        let startDate: Date = calendar.date(from: dateComponents)!
        
        let range: Range<Int> = calendar.range(of: .day, in: .month, for: startDate)!
        
        // getting date...
        return range.compactMap { day -> Date in
            return calendar.date(byAdding: .day, value: day - 1, to: startDate)!
        }
    }
    
}


```
