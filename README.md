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
		<th>Required Device</th>
		<th>Cost</th>
		<th>Purpose</th>
	</tr>
	<tr>
		<td>
			MCC 134 or other temperature capable DAQ
		</td>
		<td>
			$150 to 5000	
		</td>
		<td>
			Take temperature reading from sample to then reproduce or to take ambient temperature measurement to offset errors
		</td>
	</tr>
	<tr>
		<td>
			MCC 152 or other remote power supply	
		</td>
		<td>
   			$150 to 5000	
		</td>
		<td>
   			Output emulated TC reading to resource that consumes a temperature value
		</td>
	</tr>
	<tr>
		<td>
			Raspi, Microcontroller with gpio / i2c comms and Imaged PC	
		</td>
		<td>
   			$50 to 500	
		</td>
		<td>
      			Runs emulator software and hardware
		</td>
	</tr>
	<tr>
		<td>
			Resistors	
		</td>
		<td>
   			$20	
		</td>
		<td>
			Various resistors for voltage divider to bring the high voltage/ high resolution power supply down to the range of typical temperature measurement devices
		</td>
	</tr>
</table>



		
<h2>Simplified process flow</h2>
MCC 134 to Sample TC</br>
Convert TC data to raw voltage</br></br>

MCC 152 outputs Voltage from Analog output channel to Voltage Divider</br>
Resistor loop is used to keep open TC detection from alarming chambers or data logging hardware</br>
Resistor loop is also used in voltage divider for increasing accuracy of 152 output, due to limited resolution and voltage range.</br></br>

Hot Side (Voltages Above 0): near linear polynomial regression for fitting voltage output to requested temperature.</br></br>

<b> vout = (-0.0000001*(x**4)) + (-0.00007*(x**3))  +  (0.0153 * (x*x)) + (19.002*x) + (-425.57) </b>


Cold Side (Voltages Below 0): polynomial regression for fitting voltage output to requested temperature.

<b>vout = (-0.000015*(x**3)) + (0.0231*(x**2)) + (19.198*(x)) - 416.79</b>

Voltage Divider: Symmetrical 10k,40,10k (10k, 10,10,10,10, 10k)

Power supply channel 1 + routed to far left, channel 2 + to far right</br></br>

Medthod for error correction removed in this copy.
 

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
<h2>
	Results
</h2>
<table>
	<tr>
		<th>
			Graph
		</th>
		<th>
			Description
		</th>
	</tr>
	<tr>
		<td>
			<img src="https://github.com/micajahp/TCretransmitter/blob/6338c7ee9b93160cce23817e16b4885732725d9e/Samples.png">
		</td>
  		<td>
			Temperature Requested - Red</br>
			Actual Temperature Simulated - Green</br>
			Temperature Read - Blue
		</td>
	</tr>
 	<tr>
  		<td>
    			<img src="https://github.com/micajahp/TCretransmitter/blob/0d2f76301fd3a728e5b1821bcd1157048ce51b01/Error.png">
		</td>
		<td>
			Adjusted Error Rates With Temperature Sampling</br>
			<table>
				<tr>
					<th>
						σRMS
					</th>
					<th>
	    					μRMS
					</th>
				</tr>
				<tr>
					<td>0.049949</td>
					<td>0.055588</td>
				</tr>
			</table></br>
			Added Variance in sample from edge case hits.</br>
			Cannot simulate temperatures in excess of 227C, while sample includes requests up to 230C.
		</td>
	</tr>
</table>
<h2>
Application
</h2>
	Given accuracy and repeatablility, expected failure modes, and abort handling, hardware should only be used with product control open detection. </br>
 Errors can be handled with chamber communication where available, otherwise set error condition by open signal to product control can be used to abort testing. 

