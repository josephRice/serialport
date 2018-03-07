### SerialPort

[Docs](http://serialport.dpldocs.info/serialport.html)

Library provides simple **non-blocking** work with serial port for Posix and Windows.

Simple usage:

```d
auto com = new SerialPort("/dev/ttyUSB0", 19200);

// setting parameters example
com.config = SerialPort.Config(9600, Parity.none, DataBits.data8, StopBits.one)
com.set(19200).set(DataBits.data8);
com.stopBits = StopBits.two;
// set 9600 baudrate, 8 data bits, no parity and one stop bit
com.set("9600:8N1");

// non-blocking write -- return writed bytes count at the first onset
auto cnt = com.write(someDataArray);

// non-blocking read -- return data in system serial port buffer
auto res1 = com.read(bufferForReading);
// res1 is slice of bufferForReading with readed data
```

If you want use basic read/write loop you can use `SerialPortBL` class.

```d
//                            write timeout
com.writeLoop(someDataArray, 500.dur!"usecs");
```

At the expiration of write timeout thows `TimeoutException`

```d
//                                          read timeout    frame end gap
auto res2 = com.readLoop(bufferForReading, 500.dur!"msecs", 20.dur!"msecs");
```

At the expiration of read timeout thows `TimeoutException` if no bytes readed.
If readed bytes count != 0 wait frame end gap and if no new bytes return readed.

Example usage : [monitor](example/monitor)

#### Warning: unix systems allow only standart speeds
#### [0, 50, 75, 110, 134, 150, 200, 300, 600, 1200, 1800, 2400, 4800, 9600, 19200, 38400, 57600, 115200, 230400]

Reading and writing loops algorithms use `Fiber.yield` if available,
or `Thread.yield` otherwise. If you want redefine this behavior, you can set
`void delegate() yieldFunc` field of `SerialPort` through ctor or directly.
