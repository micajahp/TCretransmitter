# TCretransmitter

<h2>Purpose</h2>
Emulated Tc values for faster automation testing, ramp times in test can take much longer than is reasonable to wait in extreme temperature scenarios. One could emulate a much faster ramp, to check error handling on asset side or interaction layer without having to actually wait for a test article to reach desired temps. 
</br></br>
Sampling a TC to multiple locations can degrade signal quality. Using a TC for chamber control, as well as sampling requires 2 TC installed on unit. One can easily duplicate a single input to multiple outputs.</br></br>

When testing multiple units in one asset, temperature differences develop between units. To more accurately maintain temperature bounds, multiple temperature measurements can be taken from several units and one of many control strategies can be used.</br>
All average: take the temperature from all units and average, then feed to asset for control. allows overshoot/ undershoot. Generally faster as it will group all units around target though loss of individual resolution can be a problem with actual temperature bands.</br>
Highest or Lowest: Take highest temperature when approaching hot limits, and lowest when approaching low limits. Allows for zero overshoot/ undershoot. generally slower, but safer for individual units. If the test is not properly characterized, or setup and a large delta occurs between units at target temps, some units may never reach targets. 
<h2>BOM</h2>
<table>
  <tr>
    <th>
      MCC 152 or other remote power supply
    </th>
    <th>
      MCC 134 or other temperature capable DAQ
    </th>
    <th>
      Raspi, Microcontroller with gpio / i2c comms and Imaged PC
    </th>
  </tr>
  <tr>
    <th>
      $150 to 5000
    </th>
    <th>
      $150 to 5000
    </th>
    <th>
      $50 to 500
    </th>
  </tr>
  <tr>
    <th>
      Output emulated TC reading to resource that consumes a temperature value
    </th>
    <th>
      Take temperature reading from sample to then reproduce or to take ambient temperature measurement to offset errors
    </th>
    <th>
      Runs emulator software and hardware
    </th>
  </tr>
</table>
		
Resistors	$20	Various resistors for voltage divider to bring the high voltage/ high resolution power supply down to the range of typical temperature measurement devices
Simplified process flow
MCC 134 to Sample TC
Convert TC data to raw voltage

MCC 152 outputs Voltage from Analog output channel to Voltage Divider
Resistor loop is used to keep open TC detection from alarming chambers or data logging hardware
Resistor loop is also used in voltage divider for increasing accuracy of 152 output, due to limited resolution and voltage range.

Hot Side (Voltages Above 0): near linear polynomial regression for fitting voltage output to requested temperature.

vout = (-0.0000001*(x**4)) + (-0.00007*(x**3))  +  (0.0153 * (x*x)) + (19.002*x) + (-425.57)


Cold Side (Voltages Below 0): polynomial regression for fitting voltage output to requested temperature.

vout = (-0.000015*(x**3)) + (0.0231*(x**2)) + (19.198*(x)) - 416.79

Voltage Divider: Symmetrical 10k,40,10k (10k, 10,10,10,10, 10k)

Power supply channel 1 + routed to far left, channel 2 + to far right, channel 1 and 2 - to center common between center two 10 ohm
 

Adjusted_Temperature = Requested_Temperature - (Measured Error)


Feed Request for Temperature
Convert Temperature to Voltage Request
Noise Error Correction
Correction equation, MCC134 induced error from noise
Sample ambient temperature from resistor array and junction temperature at sampling device. 
Subtract Correction Value from Requested Temperature
Submit to Voltage generator as 'corrected'
Generate Voltage
Measure Temperature 
Temperature Measured - Requested = ERROR


Results


Temperature Requested - Red

Actual Temperature Simulated - Green

Temperature Read - Blue



Adjusted Error Rates With Temperature Sampling

σRMS	μRMS
0.049949	0.055588
Added Variance in sample from edge case hits.
Cannot simulate temperatures in excess of 227C, while sample includes requests up to 230C.

