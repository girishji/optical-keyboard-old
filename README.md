# optical-keyboard

Keyboard PCB for Gateron optical switches (KiCad project).

### Design

PCB is designed using a simple biasing circuit for IR Led and Phototransistor (PT). 
Both Led and PT are driven from the GPIO pin of the MCU (in this case Blackpill
STM32F401). These MCUs are readily available on AliExpress and come with USB-C
interface. 
Columns are connected to GPIO pins configured as Output. Rows are connected to
GPIO pins configured as Input but without pull-up resistor. Each column is
driven high before measuring the status (High/Low) at the row pin. After
reading all the row pins column pin is driven low and this process is repeated
for all columns. The resulting matrix is used by
  [QMK](https://github.com/girishji/qmk_firmware/tree/giri-optical-matrix/keyboards/opticalkb)
  to produce keyboard
  HID.

![image](https://i.imgur.com/qu2HqcB.png)

The resistors used are 2.2k ohm for IR Led and 47k ohm for PT. These result in
currents of ~1mA and 70uA. These are extremely low values that result in Led
being underdriven (below specs). In fact, any lower values will not make this
circuit work. The goal was to keep current draw from GPIO pins as low as
possible. Currently each column draws ~5mA (for 5 Led/PT), which is well below
the recommended maximum of 25mA per pin (of STM32F401). You can decrease the
resistor values
and increase the current, or use a transistor to drive maximum current. As a
result of low current, the rise time of PT is well below the spec of 15uSec
(here it is more like a millisecond). As a result the matrix scan rate is not
stellar (69 hz). This is acceptable for everyday typing. You can better the
scan rate by increasing current (upto 4x).

### Notes

- There is a python script that is used for placing switches and laying out
  tracks. Using this simplify your life if you intend to create your own
  keyboard layout
- My [QMK](https://github.com/girishji/qmk_firmware/tree/giri-optical-matrix/keyboards/opticalkb)
  changes are reusable; I intend to open a pull request eventually.
- There is KiCad project for switch cutout plate under `switch-coutout` directory; You can get jclpcb to make this in 1.6mm aluminum pcb
- Part numbers for JLCPCB are embedded into footprints
- Some of the footprints can he found [here](https://github.com/girishji/optical-amoeba)
- There is also a [single switch PCB](https://github.com/girishji/optical-amoeba)
- Another optical [keyboard](https://github.com/Dachtire/sok42) that might
  interest you

#### References


![image](https://i.imgur.com/hNEdrSI.jpg)
![image](https://i.imgur.com/ZMeAAxd.jpg)
![image](https://i.imgur.com/QnQpo4G.jpg)
