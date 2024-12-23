# MTH9815-Runyi-Fu-Final
### Overview

This project involves developing a bond trading system for US Treasuries, encompassing seven securities: 2Y, 3Y, 5Y, 7Y, 10Y, 20Y, and 30Y. Each security is associated with CUSIPs, coupons, and maturity dates. The system utilizes various services and data connectors to handle trading operations, market data, risk calculations, and more.

### General Components

A **ServiceListener** listens for events where data is added, updated, or removed from a service. A **Connector** manages data flow into and out of services. For example, data may be sourced from files, transmitted via sockets, or processed directly within the system. Connectors can be classified as publish-only, subscribe-only, or both publish and subscribe.

**Fractional Notation:** Prices for US Treasuries are represented in fractional notation with the smallest tick size being 1/256th. For example, “100-001” represents 100.00390625 in decimal format, and “100-25+” represents 100.796875 in decimal format. Output files should include timestamps with millisecond precision.

### Services and Their Roles

#### PricingService
Reads data from `prices.txt` and generates 1,000 prices for each security (a total of 7,000 prices across all seven securities). Prices oscillate between 99 and 101 with bid/offer spreads ranging from 1/128 to 1/64.

**Input Format:**
```
Product ID, Bid Price, Offer Price
```

#### TradeBookingService
Reads trade data from `trades.txt` to create 10 trades for each security (70 trades total). Trades alternate between BUY and SELL across books TRSY1, TRSY2, and TRSY3. This service sends trades to the PositionService via a ServiceListener.

**Input Format:**
```
Product ID, Trade ID, Price, Book, Quantity, Side
```

#### PositionService
Processes data from the TradeBookingService and calculates positions. Positions are then sent to the RiskService via a ServiceListener.

**Output Format:**
```
Timestamp, Product ID, Book, Quantity, Book, Quantity, Book, Quantity
```

#### RiskService
Calculates risk measures such as PV01 for positions received from the PositionService.

**Output Format:**
```
Timestamp, Product ID, PV01, Quantity
```

#### MarketDataService
Generates 1,000 order book updates per security (7,000 updates in total), with five levels of bid and offer stacks. Mid prices oscillate between 99 and 101, and spreads widen and tighten cyclically.

**Input Format:**
```
Product ID, Price, Quantity, Pricing Side
```

#### AlgoExecutionService
Implements an algorithm that aggresses the top of the book when the spread is at its tightest (1/128th). Execution orders are sent to the ExecutionService via a ServiceListener.

#### AlgoStreamingService
Generates bid/offer price streams and sends them to the StreamingService via a ServiceListener.

#### GUIService
Provides a graphical user interface component that throttles price updates to 300 milliseconds. Updates are written to `gui.txt`.

**Output Format:**
```
Timestamp, Product ID, Mid Price, Spread
```

#### ExecutionService
Processes execution orders received from the AlgoExecutionService and books trades through the TradeBookingService. Execution details are published via a Connector.

**Output Format:**
```
Timestamp, Product ID, Pricing Side, Order ID, Order Type, Price, Visible Quantity, Hidden Quantity, Parent Order ID, Child Order
```

#### StreamingService
Streams pricing data from the AlgoStreamingService. Output streams are published via a Connector.

**Output Format:**
```
Timestamp, Product ID, Price, Visible Quantity, Hidden Quantity, Pricing Side
```

#### InquiryService
Processes inquiries from `inquiries.txt`. Each inquiry transitions through states (RECEIVED, QUOTED, DONE) and sends quotes back via a Connector.

**Input Format:**
```
Inquiry ID, Product ID, Side, Quantity, Price, State
```
**Output Format:**
```
Inquiry ID, Product ID, Side, Quantity, Price, State
```

#### HistoricalDataService
Persists data from multiple services (PositionService, RiskService, ExecutionService, StreamingService, and InquiryService) into corresponding files such as `positions.txt`, `risk.txt`, `executions.txt`, `streaming.txt`, and `allinquiries.txt`.

### Utility Functions
Auxiliary functions are provided to facilitate various operations within the trading system.



