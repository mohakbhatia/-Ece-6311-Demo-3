# Closed Loop Response
# Ece-6311-Demo-3
clc
clear all
close all

a = arduino('com5','Mega2560') % Connecting to Arduino

shunt = readVoltage(a, 'A5'); % Initial Pot. Readings
basePot = readVoltage(a, 'A0')
shoulderPot = readVoltage(a, 'A1')
elbowPot = readVoltage(a, 'A2')
wristPot = readVoltage(a, 'A3')
grabberPot = readVoltage(a, 'A4')

%%
if shunt < 3.5 % Only runs code if shunt is removed
    % INITIAL POSITIONING. Moving arm to initial position regardless of 
    % current location
    
    % SHOULDER INITIAL POSITION
    if shoulderPot > 1.75
        shunt = readVoltage(a, 'A5');
        basePot = readVoltage(a, 'A0')
        shoulderPot = readVoltage(a, 'A1')
        elbowPot = readVoltage(a, 'A2')
        wristPot = readVoltage(a, 'A3')
        grabberPot = readVoltage(a, 'A4')
        %counter = 0;
        shoulderposition = []
        while shoulderPot > 1.75
            %counter = counter+1;
            writeDigitalPin(a,'D11',0);
            writePWMDutyCycle(a,'D4', .8);
            shoulderPot = readVoltage(a, 'A1')
            %shoulderposition(counter) = shoulderPot;
        end
        writePWMDutyCycle(a,'D4', 0);
    end   
    if shoulderPot > 1.5
        %counter = 0;
        shoulderposition = []
        while shoulderPot > 1.5
            %counter = counter+1;
            writeDigitalPin(a,'D11',0);
            writePWMDutyCycle(a,'D4', .6);
            shoulderPot = readVoltage(a, 'A1')
            %shoulderposition(counter) = shoulderPot;
        end
        writePWMDutyCycle(a,'D4', 0);
    end  
    % Correcting for Overshoot
    if shoulderPot < 1.5
        while shoulderPot < 1.5
            %counter = counter+1;
            writeDigitalPin(a,'D11',1);
            writePWMDutyCycle(a,'D4', .8);
            shoulderPot = readVoltage(a, 'A1')
            %shoulderposition(counter) = shoulderPot;
        end
        writePWMDutyCycle(a,'D4', 0);
    end
    
    pause(.5)

% % ELBOW INITIAL POSITION
    if elbowPot < 3.8
        elbowposition = []
        counter = 0
        while elbowPot < 3.8
            counter = counter + 1
            writeDigitalPin(a,'D10',0);
            writePWMDutyCycle(a,'D5', .8);
            elbowPot = readVoltage(a, 'A2')
            elbowposition(counter) = elbowPot
        end
        writePWMDutyCycle(a,'D5', 0);
    end
    if elbowPot < 4.75
        elbowposition = []
        counter = 0
        while elbowPot < 4.75
            counter = counter + 1
            writeDigitalPin(a,'D10',0);
            writePWMDutyCycle(a,'D5', .5);
            elbowPot = readVoltage(a, 'A2')
            elbowposition(counter) = elbowPot
        end
        writePWMDutyCycle(a,'D5', 0);
    end
    % Correctling for Overshoot
    elbowPot = readVoltage(a, 'A2');
    if elbowPot >= 4.75
        while elbowPot >= 4.75
            writeDigitalPin(a,'D10',1);
            writePWMDutyCycle(a,'D5', 1);
            elbowPot = readVoltage(a, 'A2')
            elbowposition(counter) = elbowPot
        end
        writePWMDutyCycle(a,'D5', 0);
    end
%  
% % WRIST INITIAL POSITION
    if wristPot < 4.29
        wristposition = []
        counter = 0
        while wristPot < 4.29
            counter = counter+1
            writeDigitalPin(a,'D12',0);
            writePWMDutyCycle(a,'D3', 1);
            wristPot = readVoltage(a, 'A3')
            wristposition(counter) = wristPot
        end
        writePWMDutyCycle(a,'D3', 0);
    end
    pause(1)
    wristPot = readVoltage(a, 'A3');

    if basePot > 1.1975
        counter = 0
        baseposition = []
        while basePot > 1.1975
            counter = counter + 1
            writeDigitalPin(a,'D9',1);
            writePWMDutyCycle(a,'D6', 1);
            basePot = readVoltage(a, 'A0');
            baseposition(counter) = basePot
        end
        writePWMDutyCycle(a,'D6', 0);
    end
    pause(1)
    basePot = readVoltage(a, 'A0');
    if basePot < 1.1975
        while basePot < 1.1975
            counter = counter + 1
            writeDigitalPin(a,'D9',0);
            writePWMDutyCycle(a,'D6', 1);
            basePot = readVoltage(a, 'A0');
            baseposition(counter) = basePot
        end
        writePWMDutyCycle(a,'D6', 0);
    end
    pause(1)

%%
% BEGIN LOOP (actual block moving sequence
% Read Voltages
shunt = readVoltage(a, 'A5');
basePot = readVoltage(a, 'A0')
shoulderPot = readVoltage(a, 'A1')
elbowPot = readVoltage(a, 'A2')
wristPot = readVoltage(a, 'A3')
grabberPot = readVoltage(a, 'A4')
  
for i = 1:5 % Loops 5 times
    % Picking up block
    if grabberPot > 4.85
        while grabberPot > 4.85
            writeDigitalPin(a,'D8',1);
            writePWMDutyCycle(a,'D7', .8);
            grabberPot = readVoltage(a, 'A4')
        end
    writePWMDutyCycle(a,'D7', 0);
    end    
    if elbowPot > 4.15
        elbowposition = []
        counter = 0
        while elbowPot > 4.15
            counter = counter + 1
            writeDigitalPin(a,'D10',1);
            writePWMDutyCycle(a,'D5', 1);
            elbowPot = readVoltage(a, 'A2')
        end
        writePWMDutyCycle(a,'D5', 0);
    end 
    pause(0.5)
    if shoulderPot < 2
        while shoulderPot < 2
            writeDigitalPin(a,'D11',1);
            writePWMDutyCycle(a,'D4', 1);
            shoulderPot = readVoltage(a, 'A1');
        end
        writePWMDutyCycle(a,'D4', 0);
    end
    pause(0.5)
    if wristPot > 3.8
        wristposition = []
        counter = 0
        while wristPot > 3.8
            counter = counter+1
            writeDigitalPin(a,'D12',1);
            writePWMDutyCycle(a,'D3', 1);
            wristPot = readVoltage(a, 'A3')
            wristposition(counter) = wristPot
        end
        writePWMDutyCycle(a,'D3', 0);
    end
    pause(0.5)
    wristPot = readVoltage(a, 'A3');
    
    % Rotating Base CCW
    if basePot < 1.9
        counter = 0
        baseposition = []
        while basePot < 1.9
            counter = counter + 1
            writeDigitalPin(a,'D9',0);
            writePWMDutyCycle(a,'D6', 1);
            basePot = readVoltage(a, 'A0');
            baseposition(counter) = basePot
        end
        writePWMDutyCycle(a,'D6', 0);
    end

    % Placing block down
    pause(0.5)
    if wristPot < 4.29
        wristposition = []
        counter = 0
        while wristPot < 4.29
            counter = counter+1
            writeDigitalPin(a,'D12',0);
            writePWMDutyCycle(a,'D3', 1);
            wristPot = readVoltage(a, 'A3')
            wristposition(counter) = wristPot
        end
        writePWMDutyCycle(a,'D3', 0);
    end
    pause(0.5)
    wristPot = readVoltage(a, 'A3');
    
    if shoulderPot > 1.75
        shunt = readVoltage(a, 'A5');
        basePot = readVoltage(a, 'A0')
        shoulderPot = readVoltage(a, 'A1')
        elbowPot = readVoltage(a, 'A2')
        wristPot = readVoltage(a, 'A3')
        grabberPot = readVoltage(a, 'A4')
        shoulderposition = []
        while shoulderPot > 1.75
            writeDigitalPin(a,'D11',0);
            writePWMDutyCycle(a,'D4', 1);
            shoulderPot = readVoltage(a, 'A1')
        end
        writePWMDutyCycle(a,'D4', 0);
    end 
    if shoulderPot > 1.53
        shoulderposition = []
        while shoulderPot > 1.53
            writeDigitalPin(a,'D11',0);
            writePWMDutyCycle(a,'D4', .6);
            shoulderPot = readVoltage(a, 'A1')
        end
        writePWMDutyCycle(a,'D4', 0);
    end  
    if shoulderPot < 1.53
        while shoulderPot < 1.53
            writeDigitalPin(a,'D11',1);
            writePWMDutyCycle(a,'D4', 1);
            shoulderPot = readVoltage(a, 'A1')
        end
        writePWMDutyCycle(a,'D4', 0);
    end
    
    pause(0.5)
    
    if elbowPot < 4.2
        elbowposition = []
        counter = 0
        while elbowPot < 4.2
            counter = counter + 1
            writeDigitalPin(a,'D10',0);
            writePWMDutyCycle(a,'D5', .8);
            elbowPot = readVoltage(a, 'A2')
            elbowposition(counter) = elbowPot
        end
        writePWMDutyCycle(a,'D5', 0);
    end
    if elbowPot < 4.75
        elbowposition = []
        counter = 0
        while elbowPot < 4.75
            counter = counter + 1
            writeDigitalPin(a,'D10',0);
            writePWMDutyCycle(a,'D5', .5);
            elbowPot = readVoltage(a, 'A2')
            elbowposition(counter) = elbowPot
        end
        writePWMDutyCycle(a,'D5', 0);
    end
    elbowPot = readVoltage(a, 'A2');
    if elbowPot >= 4.75
        while elbowPot >= 4.75
            writeDigitalPin(a,'D10',1);
            writePWMDutyCycle(a,'D5', 1);
            elbowPot = readVoltage(a, 'A2')
            elbowposition(counter) = elbowPot
        end
        writePWMDutyCycle(a,'D5', 0);
    end
    
    pause(0.5)
    
    if grabberPot < 4.95
        while grabberPot < 4.95
            writeDigitalPin(a,'D8',0);
            writePWMDutyCycle(a,'D7', 1);
            grabberPot = readVoltage(a, 'A4')
        end
    writePWMDutyCycle(a,'D7', 0);
    end    
    % Block released, pause for 15 seconds to allow tracing
    pause(15)
    
    %Picking up block
    if grabberPot > 4.85
        while grabberPot > 4.85
            writeDigitalPin(a,'D8',1);
            writePWMDutyCycle(a,'D7', .8);
            grabberPot = readVoltage(a, 'A4')
        end
    writePWMDutyCycle(a,'D7', 0);
    end    
    if elbowPot > 4.15
        elbowposition = []
        counter = 0
        while elbowPot > 4.15
            counter = counter + 1
            writeDigitalPin(a,'D10',1);
            writePWMDutyCycle(a,'D5', 1);
            elbowPot = readVoltage(a, 'A2')
        end
        writePWMDutyCycle(a,'D5', 0);
    end 
    pause(0.5)
    if shoulderPot < 2
        while shoulderPot < 2
            writeDigitalPin(a,'D11',1);
            writePWMDutyCycle(a,'D4', 1);
            shoulderPot = readVoltage(a, 'A1');
        end
        writePWMDutyCycle(a,'D4', 0);
    end
    pause(0.5)
    if wristPot > 3.8
        wristposition = []
        counter = 0
        while wristPot > 3.8
            counter = counter+1
            writeDigitalPin(a,'D12',1);
            writePWMDutyCycle(a,'D3', 1);
            wristPot = readVoltage(a, 'A3')
            wristposition(counter) = wristPot
        end
        writePWMDutyCycle(a,'D3', 0);
    end
    pause(0.5)
    wristPot = readVoltage(a, 'A3');
    
    if basePot > 1.1975
        counter = 0
        baseposition = []
        while basePot > 1.1975
            counter = counter + 1
            writeDigitalPin(a,'D9',1);
            writePWMDutyCycle(a,'D6', 1);
            basePot = readVoltage(a, 'A0');
            baseposition(counter) = basePot
        end
        writePWMDutyCycle(a,'D6', 0);
    end
    basePot = readVoltage(a, 'A0');
    if basePot < 1.1975
        while basePot < 1.1975
            counter = counter + 1
            writeDigitalPin(a,'D9',0);
            writePWMDutyCycle(a,'D6', 1);
            basePot = readVoltage(a, 'A0');
            baseposition(counter) = basePot
        end
        writePWMDutyCycle(a,'D6', 0);
    end
    pause(0.5)
    
    if wristPot < 4.29
        wristposition = []
        counter = 0
        while wristPot < 4.29
            counter = counter+1
            writeDigitalPin(a,'D12',0);
            writePWMDutyCycle(a,'D3', 1);
            wristPot = readVoltage(a, 'A3')
            wristposition(counter) = wristPot
        end
        writePWMDutyCycle(a,'D3', 0);
    end
    pause(0.5)
    wristPot = readVoltage(a, 'A3');
    
    if shoulderPot > 1.75
        shunt = readVoltage(a, 'A5');
        basePot = readVoltage(a, 'A0')
        shoulderPot = readVoltage(a, 'A1')
        elbowPot = readVoltage(a, 'A2')
        wristPot = readVoltage(a, 'A3')
        grabberPot = readVoltage(a, 'A4')
        shoulderposition = []
        while shoulderPot > 1.75
            writeDigitalPin(a,'D11',0);
            writePWMDutyCycle(a,'D4', 1);
            shoulderPot = readVoltage(a, 'A1')
        end
        writePWMDutyCycle(a,'D4', 0);
    end 
    
    if shoulderPot > 1.53
        shoulderposition = []
        while shoulderPot > 1.53
            writeDigitalPin(a,'D11',0);
            writePWMDutyCycle(a,'D4', .6);
            shoulderPot = readVoltage(a, 'A1')
        end
        writePWMDutyCycle(a,'D4', 0);
    end  
    if shoulderPot < 1.53
        while shoulderPot < 1.53
            writeDigitalPin(a,'D11',1);
            writePWMDutyCycle(a,'D4', 1);
            shoulderPot = readVoltage(a, 'A1')
        end
        writePWMDutyCycle(a,'D4', 0);
    end
    
    pause(0.5)
    
    if elbowPot < 4.2
        elbowposition = []
        counter = 0
        while elbowPot < 4.2
            counter = counter + 1
            writeDigitalPin(a,'D10',0);
            writePWMDutyCycle(a,'D5', .8);
            elbowPot = readVoltage(a, 'A2')
            elbowposition(counter) = elbowPot
        end
        writePWMDutyCycle(a,'D5', 0);
    end
    if elbowPot < 4.75
        elbowposition = []
        counter = 0
        while elbowPot < 4.75
            counter = counter + 1
            writeDigitalPin(a,'D10',0);
            writePWMDutyCycle(a,'D5', .5);
            elbowPot = readVoltage(a, 'A2')
            elbowposition(counter) = elbowPot
        end
        writePWMDutyCycle(a,'D5', 0);
    end

    elbowPot = readVoltage(a, 'A2');
    if elbowPot >= 4.75
        while elbowPot >= 4.75
            writeDigitalPin(a,'D10',1);
            writePWMDutyCycle(a,'D5', 1);
            elbowPot = readVoltage(a, 'A2')
            elbowposition(counter) = elbowPot
        end
        writePWMDutyCycle(a,'D5', 0);
    end
    if grabberPot < 4.95
        while grabberPot < 4.95
            writeDigitalPin(a,'D8',0);
            writePWMDutyCycle(a,'D7', 1);
            grabberPot = readVoltage(a, 'A4')
        end
    writePWMDutyCycle(a,'D7', 0);
    end    
    
    % Block back in (hopefully) original position. Pause to trace
    pause(15)
end
