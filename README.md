# Simulation-of-energy-management-algorithm-using-MATLAB
## AIM
To Simulate energy management algorithm using MATLAB

## APPARATUS REQUIRED
•	MATLAB

## MATLAB CODING
% Energy Management System Simulation in MATLAB

clc; clear; close all;

%% Simulation Parameters

t = 0:1:23; % time in hours (24-hour simulation)

% Sample profiles (simplified)

load_demand = [2.5 2.4 2.3 2.2 2.1 2.0 2.5 3.5 4.0 4.5 5.0 5.5 ... 5.8 5.6 5.4 5.0 4.5 4.0 3.5 3.0 2.8 2.6 2.4 2.3]; % in kW

pv_generation = [0 0 0 0 0.5 1.0 2.0 3.5 4.0 5.5 6.0 5.5 ... 5.0 4.0 3.0 2.0 1.0 0.5 0 0 0 0 0 0]; % in kW

% Battery Parameters

battery_capacity = 10; % in kWh

battery_SOC = 0.5; % initial SOC (50%)

battery_min_SOC = 0.2; % lower limit

battery_max_SOC = 0.9; % upper limit

battery_power_max = 3; % max charge/discharge power in kW

% Outputs

grid_power = zeros(1, length(t)); % power drawn from grid

battery_power = zeros(1, length(t));

SOC_log = zeros(1, length(t));

%% Energy Management Algorithm

for i = 1:length(t)

load = load_demand(i);

pv = pv_generation(i);

net_power = pv - load; % net power (positive = surplus, negative = deficit)

if net_power >= 0

% Surplus PV: try to charge battery

if battery_SOC < battery_max_SOC

charge_power = min([net_power, battery_power_max, (battery_max_SOC - battery_SOC) * battery_capacity]);

battery_power(i) = charge_power; % battery charging

battery_SOC = battery_SOC + (charge_power / battery_capacity);

grid_power(i) = 0;

else

battery_power(i) = 0; % battery full

grid_power(i) = 0; % export to grid ignored

end

else

% Deficit: try to discharge battery first

deficit = -net_power;

if battery_SOC > battery_min_SOC

discharge_power = min([deficit, battery_power_max,(battery_SOC - battery_min_SOC) * battery_capacity]);

battery_power(i) = -discharge_power; % battery discharging

battery_SOC = battery_SOC - (discharge_power / battery_capacity);

grid_power(i) = deficit - discharge_power;

else

battery_power(i) = 0; % battery empty

grid_power(i) = deficit; % rest from grid

end

end

% Clamp SOC

battery_SOC = min(max(battery_SOC, battery_min_SOC), battery_max_SOC);

SOC_log(i) = battery_SOC;

end

%% Plot Results

figure;

subplot(4,1,1);

plot(t, load_demand, 'r', 'LineWidth', 1.5); hold on;

plot(t, pv_generation, 'g', 'LineWidth', 1.5);

ylabel('Power (kW)');

legend('Load Demand', 'PV Generation');

title('Load and PV Profiles'); grid on;

subplot(4,1,2);

plot(t, battery_power, 'b', 'LineWidth', 1.5);

ylabel('Battery Power (kW)');

title('Battery Charge/Discharge Profile');

grid on;

subplot(4,1,3);

plot(t, grid_power, 'm', 'LineWidth', 1.5);

ylabel('Grid Power (kW)');

title('Power Drawn from Grid');

grid on;

subplot(4,1,4);

plot(t, SOC_log * 100, 'k', 'LineWidth', 1.5);

xlabel('Time (Hours)');

ylabel('SOC (%)');

title('Battery State of Charge');

grid on;

## OUTPUT

<img width="1600" height="847" alt="image" src="https://github.com/user-attachments/assets/27351047-6ff9-49b7-911a-726d7c46b01d" />

## RESULT

The energy management algorithm was successfully simulated in MATLAB, ensuring optimal power flow between load, PV, battery, and grid.
