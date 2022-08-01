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

![image](https://i.imgur.com/vM64ZgA.png)

The resistors used are 2.2k ohm for IR Led and 47k ohm for PT. These result in
currents of ~1mA and 70uA. These are extremely low values that result in Led
being underdriven (below specs). In fact, any lower values will not make this
circuit work. The goal was to keep current draw from GPIO pins as low as
possible. Currently each column draws ~5mA (for 5 Led/PT), which is well below
the recommended maximum of 25mA per pin (of STM32F401). 

There is a tradeoff between sensitivity and response time of PT. Higher current
at IR Led leads to more collector current at PT. Higer resistor next to PT will
increase its sensitivity (smaller collector current is needed to accomplish
switching operation). However, higher value of resistor also decreases response
time.

![image](https://i.imgur.com/ZBZfLN9.png)

The rise time of PT is generally around 15uSec. We would like to wait no more
than this time during matrix scan to read the input pins (rows). This gives us
the best matrix scan frequency. When using a 3v3 IO pin to power IR Led (say
Blackpill STM32F4) through a 2.2K resistor, we need at least 47K resistor at PT
to achieve reliable switching. When using 1K resistor at Led, we need 15K
resistor (at least) to achieve fast switching (without jitteriness). The
current draw at IR Leds in this case is around 11mA (voltage drop is around
2.14V across the Led resistor). Similarly, when using 680R resistor we need at
least 8k resistor at PT, with current draw of around 16mA for GPIO pin.

Since extremely fast switching time can be achieved with minimal current there
is no need to use transistors to boost current; GPIO pin provides sufficient
current, even if it means IR Led is underdriven. To gain control over the
sensitivity of PT it is better not to use the built-in pull-up resistor of IO
pin (which has fixed value, around 30k or 40k ohms).


### Notes

- There is a python script that is used for placing switches and laying out
  tracks. Using this simplify your life if you intend to create your own
  keyboard layout
- My [QMK](https://github.com/girishji/qmk_firmware/tree/giri-optical-matrix/keyboards/opticalkb)
  changes are reusable; I intend to open a pull request eventually.
- There is KiCad project for switch cutout plate under `switch-coutout` directory; You can get jclpcb to make this in 1.6mm aluminum pcb
- Part numbers for JLCPCB are embedded into footprints
- There is also a [single switch PCB](https://github.com/girishji/optical-amoeba)
- Another optical [keyboard](https://github.com/Dachtire/sok42) that might interest you

#### References


![image](https://i.imgur.com/hNEdrSI.jpg)
![image](https://i.imgur.com/ZMeAAxd.jpg)
![image](https://i.imgur.com/QnQpo4G.jpg)
