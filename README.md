#include <iostream>
#include <vector>
#include <map>
#include <fstream>
#include <sstream>
#include <random>
#include <chrono>
#include <thread>

using namespace std;

// Sensor Data Structure
struct SensorData {
    string sensorId;
    string timestamp;
    double value;
};

// Crop Information Structure
struct Crop {
    string name;
    vector<SensorData> sensorReadings;
    double optimalTemperature;
    double optimalHumidity;
    double optimalLight;
};

// Intelligent Agriculture System Class
class IntelligentAgricultureSystem {
private:
    vector<Crop> crops;
    map<string, SensorData> sensorReadings;
    random_device rd;
    mt19937 gen;
    uniform_real_distribution<> distrib;

public:
    // Constructor
    IntelligentAgricultureSystem() : gen(rd()), distrib(0.0, 1.0) {}

    // Add a new crop
    void addCrop(const string& name, double optimalTemperature, double optimalHumidity, double optimalLight) {
        crops.push_back({name, {}, optimalTemperature, optimalHumidity, optimalLight});
    }

    // Simulate sensor readings
    void simulateSensorReadings() {
        for (Crop& crop : crops) {
            for (string sensorId : {"temperature", "humidity", "light"}) {
                double value = distrib(gen);
                // Adjust based on optimal values (simplification for demo)
                value = (value + 0.5) * crop.optimalTemperature;
                if (sensorId == "humidity") {
                    value = (value + 0.5) * crop.optimalHumidity;
                } else if (sensorId == "light") {
                    value = (value + 0.5) * crop.optimalLight;
                }
                sensorReadings[sensorId] = {sensorId, getCurrentTimestamp(), value};
                crop.sensorReadings.push_back({sensorId, getCurrentTimestamp(), value});
            }
        }
    }

    // Get current timestamp
    string getCurrentTimestamp() {
        auto now = chrono::system_clock::now();
        time_t time = chrono::system_clock::to_time_t(now);
        return ctime(&time);
    }

    // Analyze sensor data for a crop
    void analyzeCropData(const Crop& crop) {
        cout << "Analyzing data for crop: " << crop.name << endl;
        for (const SensorData& reading : crop.sensorReadings) {
            cout << "  " << reading.sensorId << ": " << reading.value << " (" << reading.timestamp << ")" << endl;
        }
        // Analyze readings against optimal values, and implement actions (not shown here)
        // e.g.,
        // if (reading.value < crop.optimalTemperature) {
        //     cout << "  Warning: Temperature is below optimal!" << endl;
        // } 
    }

    // Analyze sensor data for all crops
    void analyzeAllCropData() {
        for (const Crop& crop : crops) {
            analyzeCropData(crop);
            cout << "-------------------" << endl;
        }
    }

    // Save sensor data to file (not shown here)
    // void saveSensorData(const string& filename) {
    //     // ...
    // }
};

int main() {
    IntelligentAgricultureSystem system;

    // Add crops
    system.addCrop("Tomato", 25.0, 60.0, 1000.0); // Example: optimal values
    system.addCrop("Lettuce", 20.0, 70.0, 800.0);

    // Simulate sensor data
    system.simulateSensorReadings();

    // Analyze crop data
    system.analyzeAllCropData();

    // Simulate data collection over time
    while (true) {
        this_thread::sleep_for(chrono::seconds(5)); // Simulate data collection interval
        system.simulateSensorReadings();
        system.analyzeAllCropData();
        // system.saveSensorData("sensor_data.txt"); // Optionally save data
    }

    return 0;
}
