# Current sensing by a modified ZMCT103C module

Original PCB

  ![Original PCB](pic/ZMCT103C_module.png)  

Modified PCB

  ![Modified PCB](pic/ZMCT103C_module_modified.png)  

Original schematic

  ![Original PCB](pic/SchemOrig.png)  

Modified schematic

  ![Original PCB](pic/Schemmod.png)  

  ZMCT103C datasheet [ZMCT103C datasheet](https://5nrorwxhmqqijik.leadongcdn.com/ZMCT103C+specification-aidirBqoKomRilSjpimnokp.pdf)

### I analyzed the current sensor module purchased on eBay a bit before using it.

When purchasing, I relied on the description, and in hindsight, it is clear that the 5A rating in the product title is an exaggeration. Since the price of the module was low, I didn't think twice about buying it. However, before putting it to use, I wanted to see what kind of performance I could expect.

Here is an example of such a module:
[https://www.ebay.com/itm/236746382331](https://www.ebay.com/itm/236746382331)

According to the description, it is supposed to measure current up to 5A. In reality, however, it does not come anywhere near this value.

### Using the module in an Arduino project for current measurement without modifications is pointless.

The reason for this is that there is a capacitor coupling at the output point. This prevents the DC signal from passing through. Consequently, there is no voltage offset to shift the output signal of the circuit (the input signal to the ADC) into the positive range. The ADC gives the same response for values below 0V, which is zero. It clips the negative values from the signal. What remains of a sine wave is only the positive side.

### The operational amplifier used cannot swing its output all the way up to the positive supply voltage.

According to its datasheet, the maximum output value stays roughly 1.5V below the supply voltage. If the board is powered with $V_{cc} = 5\text{V}$, the magnitude of the positive peaks at the output is: $(V_{cc} - 1.5\text{V}) - V_{cc}/2 = V_{cc}/2 - 1.5\text{V} = 1\text{V}$. Therefore, the maximum possible output value is 1V. Due to the capacitor coupling, it will be slightly higher than this, since the voltage level at the output of the operational amplifier extends downwards into the 2.5V range: $(\text{Peak-to-peak}) / 2$. From the ADC's perspective, this means that the maximum value is approximately $1.75 \times 1023 / 5\text{V} = 358$. If there is no pull-up or pull-down resistor on the analog input of the Arduino (UNO, Nano), interference sources may further affect this.

### The first stage enters saturation at a current of 1A.

The current transformer has a 1:1000 turns ratio (nominal output current is 5mA for 5A). The gain of the first stage is: $100\text{k} / 10\text{k} = 10$. The aforementioned 1V output occurs at a 100mV input (above this, the circuit clips the signal). A current of 1mA across a 100-ohm input resistor produces a voltage of 100mV. It follows that for currents greater than 1A through the current transformer, the circuit saturates and delivers a distorted signal at its output.

---  

  ### Az Ebay-en vásárolt áramérzékelő modult felhasználás előtt kicsit elemeztem. 

A vásárlásnál a leírásra hagyatkoztam és utólag látszik, a megnevezésben az 5A túlzás. Mivel a modul ára alacsony sokáig nem gondolkoztam rajta, hogy vásároljak-e belőle. Felhasználáskor viszont szerettem volna látni, milyen működésre számíthatok.

Példának egy ilyen modul:

https://www.ebay.com/itm/236746382331

A megnevezés szerint 5A-ig kellene áramot mérnie. A valóságban azonban meg sem közelíti ezt az értéket. 

### A modult módosítás nélkül arduino projektben árammérésre használni értelmetlen. 

Ennek az az oka, hogy a kimeneti ponton egy kondenzátoros csatolás található. Ez megakadályozza a DC jel áthaladását. Így tehát nincs feszültség offszet, ami a kapcsolásból kijövő jelet (ADC-be bemenő jelet) a pozitív tartományba helyezné. Az ADC 0V alatti értékekre azonos választ ad, azaz nulla. Levágja a jelből a negatív értékeket. Ami megmarad egy szinusz hullámból, az a pozitív oldal. 

###  Az alkalmazott műveleti erősítő nem tudja a kimenetét a pozitív tápfeszültségig eltolni. 

Az adatlapja szerint a tápfeszültség alatt marad nagyjából 1,5V-tal a maximális kimeneti érték. Ha a panelt megtápláljuk Vcc=5V-tal, akkor a kimeneten a pozitív csúcsok mértéke: (Vcc - 1,5V) - Vcc/2 = Vcc/2 - 1,5V = 1V. A lehetséges maximális kimeneti érték tehát 1V. A kondenzátoros csatolás miatt ettől kissé nagyobb lesz, mivel a feszültség mértéke a műveleti erősítő kimenetén lefelé a 2,5V tartományban van. ( Csúcstól csúcsig ) / 2. ADC szempontjából ez azt jelenti, hogy a maximális érték kb. 1,75 x 1023 / 5V = 358. Ha az arduino (UNO,nano) analog bemenetén nincs felhúzó vagy lehúzó ellenállás, akkor zavarforrások ezt még befolyásolhatják.

### Az első fokozat telítésbe megy 1A áram esetén.

Az áramváltó 1:1000 áttételű ( névleges kimeneti áram 5mA -> 5A hatására ). Az első fokozat erősítése: 100k/10k = 10. A fenti 1V kimenet 100mV bemenetnél adódik (e fölött már vágja a jelet a kapcsolás). 100ohm bemeneti ellenálláson 1mA áram hatására 100mV feszültség adódik. Innen következik, hogy az áramváltón 1A-nél nagyobb áramok esetében az áramkör telítődik, torzított jelet ad a kimenetén. 

### TASMOTA
A bemenetet ADC-CTpower-re kell állítani a "Configuration" menüben, majd konzolban:
ADCParam 17,0,212,0.235,0





