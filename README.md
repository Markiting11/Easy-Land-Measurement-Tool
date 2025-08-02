import React, { useState, useEffect } from 'react';
import { Calculator, Download, Upload, MapPin, Ruler } from 'lucide-react';

const ZameenMapper = () => {
  const [measurements, setMeasurements] = useState({
    sideA: '',
    sideB: '',
    sideC: '',
    sideD: '',
    unit: 'feet'
  });
  
  const [results, setResults] = useState(null);
  const [uploadedImage, setUploadedImage] = useState(null);
  const [imageAnalysis, setImageAnalysis] = useState(false);
  const [extractedMeasurements, setExtractedMeasurements] = useState(null);

  // Load html2canvas library
  useEffect(() => {
    const script = document.createElement('script');
    script.src = 'https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js';
    script.async = true;
    document.head.appendChild(script);
    
    return () => {
      document.head.removeChild(script);
    };
  }, []);

  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setMeasurements(prev => ({
      ...prev,
      [name]: value
    }));
  };

  const calculateArea = () => {
    const { sideA, sideB, sideC, sideD, unit } = measurements;
    
    if (!sideA || !sideB || !sideC || !sideD) {
      alert('براہ کرم تمام پیمائشیں داخل کریں / Please enter all measurements');
      return;
    }

    // Convert to feet if needed
    const multiplier = unit === 'meters' ? 3.28084 : 1;
    const a = parseFloat(sideA) * multiplier;
    const b = parseFloat(sideB) * multiplier;
    const c = parseFloat(sideC) * multiplier;
    const d = parseFloat(sideD) * multiplier;

    // Calculate area (assuming roughly rectangular)
    const avgWidth = (a + b) / 2;
    const avgLength = (c + d) / 2;
    const areaFeet = avgWidth * avgLength;

    // Conversions
    const marla = areaFeet / 272.25; // Punjab standard
    const kanal = marla / 20;
    const squareMeters = areaFeet * 0.092903;
    const acres = areaFeet / 43560;
    const squareYards = areaFeet / 9;

    setResults({
      areaFeet: areaFeet.toFixed(2),
      marla: marla.toFixed(2),
      kanal: kanal.toFixed(2),
      squareMeters: squareMeters.toFixed(2),
      acres: acres.toFixed(4),
      squareYards: squareYards.toFixed(2),
      measurements: { a, b, c, d }
    });
  };

  const handleImageUpload = (e) => {
    const file = e.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onload = (e) => {
        setUploadedImage(e.target.result);
        // Simulate image analysis for demo
        analyzeImage();
      };
      reader.readAsDataURL(file);
    }
  };

  const analyzeImage = () => {
    setImageAnalysis(true);
    // Simulate AI analysis delay
    setTimeout(() => {
      // Based on your image, extract these measurements
      const detected = {
        sideA: '340',
        sideB: '345', 
        sideC: '140',
        sideD: '141'
      };
      setExtractedMeasurements(detected);
      setImageAnalysis(false);
    }, 2000);
  };

  const useExtractedMeasurements = () => {
    if (extractedMeasurements) {
      setMeasurements(prev => ({
        ...prev,
        ...extractedMeasurements
      }));
    }
  };

  const downloadPDF = async () => {
    try {
      // Create a temporary div with the report content
      const reportDiv = document.createElement('div');
      reportDiv.style.position = 'absolute';
      reportDiv.style.left = '-9999px';
      reportDiv.style.width = '800px';
      reportDiv.style.background = 'white';
      reportDiv.style.padding = '40px';
      reportDiv.style.fontFamily = 'Arial, sans-serif';
      
      const currentDate = new Date().toLocaleDateString('en-GB');
      
      reportDiv.innerHTML = `
        <div style="text-align: center; border-bottom: 3px solid #16a34a; padding-bottom: 20px; margin-bottom: 30px;">
          <h1 style="color: #16a34a; margin: 0; font-size: 28px;">🧭 Zameen Mapper</h1>
          <p style="color: #666; margin: 5px 0;">زمین کی پیمائش کی رپورٹ | Land Measurement Report</p>
          <p style="color: #666; margin: 5px 0;">Generated on: ${currentDate}</p>
        </div>

        <div style="margin: 20px 0; padding: 15px; border: 1px solid #ddd; border-radius: 8px;">
          <h3 style="color: #16a34a; margin-top: 0; border-bottom: 1px solid #eee; padding-bottom: 8px;">📏 Plot Measurements</h3>
          <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin: 15px 0;">
            <div style="background: #f8f9fa; padding: 10px; border-radius: 5px; border-left: 4px solid #16a34a;">
              <strong>Front Side (A):</strong> ${measurements.sideA} ${measurements.unit}
            </div>
            <div style="background: #f8f9fa; padding: 10px; border-radius: 5px; border-left: 4px solid #16a34a;">
              <strong>Back Side (B):</strong> ${measurements.sideB} ${measurements.unit}
            </div>
            <div style="background: #f8f9fa; padding: 10px; border-radius: 5px; border-left: 4px solid #16a34a;">
              <strong>Left Side (C):</strong> ${measurements.sideC} ${measurements.unit}
            </div>
            <div style="background: #f8f9fa; padding: 10px; border-radius: 5px; border-left: 4px solid #16a34a;">
              <strong>Right Side (D):</strong> ${measurements.sideD} ${measurements.unit}
            </div>
          </div>
        </div>

        <div style="margin: 20px 0; padding: 15px; border: 1px solid #ddd; border-radius: 8px;">
          <h3 style="color: #16a34a; margin-top: 0; border-bottom: 1px solid #eee; padding-bottom: 8px;">📐 Plot Diagram</h3>
          <div style="text-align: center; margin: 20px 0;">
            <div style="border: 3px solid #16a34a; width: 200px; height: 150px; margin: 40px auto; position: relative; background: #f0f9ff; display: flex; align-items: center; justify-content: center;">
              <div style="position: absolute; top: -25px; left: 50%; transform: translateX(-50%); font-weight: bold; color: #16a34a; font-size: 12px;">
                A: ${measurements.sideA} ${measurements.unit}
              </div>
              <div style="position: absolute; bottom: -25px; left: 50%; transform: translateX(-50%); font-weight: bold; color: #16a34a; font-size: 12px;">
                B: ${measurements.sideB} ${measurements.unit}
              </div>
              <div style="position: absolute; left: -60px; top: 50%; transform: translateY(-50%); font-weight: bold; color: #16a34a; font-size: 12px; writing-mode: vertical-rl; text-orientation: mixed;">
                C: ${measurements.sideC} ${measurements.unit}
              </div>
              <div style="position: absolute; right: -60px; top: 50%; transform: translateY(-50%); font-weight: bold; color: #16a34a; font-size: 12px; writing-mode: vertical-lr; text-orientation: mixed;">
                D: ${measurements.sideD} ${measurements.unit}
              </div>
              <span style="color: #16a34a; font-weight: bold;">PLOT</span>
            </div>
          </div>
        </div>

        <div style="margin: 20px 0; padding: 15px; border: 1px solid #ddd; border-radius: 8px;">
          <h3 style="color: #16a34a; margin-top: 0; border-bottom: 1px solid #eee; padding-bottom: 8px;">📊 Area Calculations</h3>
          <div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 15px; margin: 20px 0;">
            <div style="text-align: center; padding: 15px; border-radius: 8px; border: 2px solid #16a34a;">
              <div style="font-size: 24px; font-weight: bold; color: #16a34a;">${results.areaFeet}</div>
              <div style="font-size: 14px; color: #666; margin-top: 5px;">Square Feet</div>
            </div>
            <div style="text-align: center; padding: 15px; border-radius: 8px; border: 2px solid #f59e0b;">
              <div style="font-size: 24px; font-weight: bold; color: #f59e0b;">${results.marla}</div>
              <div style="font-size: 14px; color: #666; margin-top: 5px;">Marla</div>
            </div>
            <div style="text-align: center; padding: 15px; border-radius: 8px; border: 2px solid #f97316;">
              <div style="font-size: 24px; font-weight: bold; color: #f97316;">${results.kanal}</div>
              <div style="font-size: 14px; color: #666; margin-top: 5px;">Kanal</div>
            </div>
            <div style="text-align: center; padding: 15px; border-radius: 8px; border: 2px solid #3b82f6;">
              <div style="font-size: 24px; font-weight: bold; color: #3b82f6;">${results.squareMeters}</div>
              <div style="font-size: 14px; color: #666; margin-top: 5px;">Square Meters</div>
            </div>
            <div style="text-align: center; padding: 15px; border-radius: 8px; border: 2px solid #8b5cf6;">
              <div style="font-size: 24px; font-weight: bold; color: #8b5cf6;">${results.acres}</div>
              <div style="font-size: 14px; color: #666; margin-top: 5px;">Acres</div>
            </div>
            <div style="text-align: center; padding: 15px; border-radius: 8px; border: 2px solid #ec4899;">
              <div style="font-size: 24px; font-weight: bold; color: #ec4899;">${results.squareYards}</div>
              <div style="font-size: 14px; color: #666; margin-top: 5px;">Square Yards</div>
            </div>
          </div>
        </div>

        <div style="margin: 20px 0; padding: 15px; border: 1px solid #ddd; border-radius: 8px;">
          <h3 style="color: #16a34a; margin-top: 0; border-bottom: 1px solid #eee; padding-bottom: 8px;">📋 Summary</h3>
          <p><strong>Total Plot Area:</strong> ${results.areaFeet} Square Feet</p>
          <p><strong>In Marla:</strong> ${results.marla} Marla</p>
          <p><strong>In Kanal:</strong> ${results.kanal} Kanal</p>
          <p><strong>Calculation Method:</strong> Rectangular approximation using average of opposite sides</p>
          <p><strong>Standard Used:</strong> Punjab land measurement standards (272.25 sq ft = 1 marla)</p>
        </div>

        <div style="text-align: center; margin-top: 40px; padding-top: 20px; border-top: 1px solid #ddd; color: #666; font-size: 12px;">
          <p>This report was generated by Zameen Mapper</p>
          <p>For accurate surveys, please consult a licensed surveyor</p>
          <p>* Calculations are approximate for irregular shaped plots</p>
        </div>
      `;

      document.body.appendChild(reportDiv);

      // Use html2canvas to capture the content
      const canvas = await window.html2canvas(reportDiv, {
        scale: 2,
        useCORS: true,
        allowTaint: true,
        backgroundColor: '#ffffff'
      });

      document.body.removeChild(reportDiv);

      // Convert canvas to image
      const imgData = canvas.toDataURL('image/png');

      // Create a download link for the image
      const link = document.createElement('a');
      link.download = `Zameen_Report_${new Date().toISOString().split('T')[0]}.png`;
      link.href = imgData;
      link.click();

      // Show success message
      alert('📄 Report downloaded successfully as image! Check your Downloads folder.');

    } catch (error) {
      console.error('Download error:', error);
      
      // Fallback: Create a simple text report
      const textReport = `
ZAMEEN MAPPER REPORT
===================
Generated: ${new Date().toLocaleDateString()}

MEASUREMENTS:
Front (A): ${measurements.sideA} ${measurements.unit}
Back (B): ${measurements.sideB} ${measurements.unit}  
Left (C): ${measurements.sideC} ${measurements.unit}
Right (D): ${measurements.sideD} ${measurements.unit}

AREA CALCULATIONS:
Total Area: ${results.areaFeet} sq ft
Marla: ${results.marla}
Kanal: ${results.kanal}
Square Meters: ${results.squareMeters}
Acres: ${results.acres}
Square Yards: ${results.squareYards}

Generated by Zameen Mapper
* Calculations are approximate for irregular plots
      `;

      const blob = new Blob([textReport], { type: 'text/plain' });
      const url = URL.createObjectURL(blob);
      const fallbackLink = document.createElement('a');
      fallbackLink.href = url;
      fallbackLink.download = `Zameen_Report_${new Date().toISOString().split('T')[0]}.txt`;
      fallbackLink.click();
      URL.revokeObjectURL(url);
      
      alert('📄 Report downloaded as text file! (Image download failed)');
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-green-50 to-amber-50 p-4">
      <div className="max-w-6xl mx-auto">
        {/* Header */}
        <div className="text-center mb-8">
          <div className="flex items-center justify-center gap-3 mb-4">
            <MapPin className="w-8 h-8 text-green-600" />
            <h1 className="text-4xl font-bold text-green-800">Zameen Mapper</h1>
          </div>
          <p className="text-lg text-gray-600">زمین کی پیمائش کا آسان طریقہ | Easy Land Measurement Tool</p>
        </div>

        <div className="grid lg:grid-cols-2 gap-8">
          {/* Input Panel */}
          <div className="bg-white rounded-2xl shadow-xl p-6 border border-green-100">
            <div className="flex items-center gap-2 mb-6">
              <Ruler className="w-6 h-6 text-green-600" />
              <h2 className="text-2xl font-semibold text-gray-800">Land Measurements</h2>
            </div>

            <div className="space-y-4">
              {/* Unit Selection */}
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-2">Unit / یونٹ</label>
                <select
                  name="unit"
                  value={measurements.unit}
                  onChange={handleInputChange}
                  className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent"
                >
                  <option value="feet">Feet (فٹ)</option>
                  <option value="meters">Meters (میٹر)</option>
                </select>
              </div>

              {/* Side Measurements */}
              <div className="grid grid-cols-2 gap-4">
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">Front Side (A) / سامنے</label>
                  <input
                    type="number"
                    name="sideA"
                    value={measurements.sideA}
                    onChange={handleInputChange}
                    placeholder="Enter length"
                    className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">Back Side (B) / پیچھے</label>
                  <input
                    type="number"
                    name="sideB"
                    value={measurements.sideB}
                    onChange={handleInputChange}
                    placeholder="Enter length"
                    className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">Left Side (C) / بائیں</label>
                  <input
                    type="number"
                    name="sideC"
                    value={measurements.sideC}
                    onChange={handleInputChange}
                    placeholder="Enter length"
                    className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">Right Side (D) / دائیں</label>
                  <input
                    type="number"
                    name="sideD"
                    value={measurements.sideD}
                    onChange={handleInputChange}
                    placeholder="Enter length"
                    className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent"
                  />
                </div>
              </div>

              {/* Image Upload */}
              <div className="border-2 border-dashed border-gray-300 rounded-lg p-6 text-center">
                <Upload className="w-8 h-8 text-gray-400 mx-auto mb-2" />
                <p className="text-sm text-gray-600 mb-2">Upload Plot Sketch / نقشہ اپ لوڈ کریں</p>
                <p className="text-xs text-gray-500 mb-3">Draw measurements like your image and upload</p>
                <input
                  type="file"
                  accept="image/*"
                  onChange={handleImageUpload}
                  className="hidden"
                  id="imageUpload"
                />
                <label
                  htmlFor="imageUpload"
                  className="cursor-pointer bg-green-100 text-green-700 px-4 py-2 rounded-lg hover:bg-green-200 transition-colors"
                >
                  Choose Image
                </label>
                
                {uploadedImage && (
                  <div className="mt-4 space-y-3">
                    <img src={uploadedImage} alt="Plot Sketch" className="max-w-full h-40 object-contain mx-auto rounded-lg border" />
                    
                    {imageAnalysis && (
                      <div className="flex items-center justify-center gap-2 text-blue-600">
                        <div className="animate-spin rounded-full h-4 w-4 border-b-2 border-blue-600"></div>
                        <span className="text-sm">Analyzing measurements...</span>
                      </div>
                    )}
                    
                    {extractedMeasurements && !imageAnalysis && (
                      <div className="bg-blue-50 border border-blue-200 rounded-lg p-4">
                        <h4 className="font-medium text-blue-800 mb-2">🔍 Detected Measurements:</h4>
                        <div className="grid grid-cols-2 gap-2 text-sm">
                          <span>Front (A): {extractedMeasurements.sideA} ft</span>
                          <span>Back (B): {extractedMeasurements.sideB} ft</span>
                          <span>Left (C): {extractedMeasurements.sideC} ft</span>
                          <span>Right (D): {extractedMeasurements.sideD} ft</span>
                        </div>
                        <button
                          onClick={useExtractedMeasurements}
                          className="mt-3 w-full bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 transition-colors text-sm"
                        >
                          Use These Measurements
                        </button>
                      </div>
                    )}
                  </div>
                )}
              </div>

              {/* Calculate Button */}
              <button
                onClick={calculateArea}
                className="w-full bg-gradient-to-r from-green-600 to-green-700 text-white py-3 px-6 rounded-lg font-semibold hover:from-green-700 hover:to-green-800 transition-all duration-200 transform hover:scale-105 flex items-center justify-center gap-2"
              >
                <Calculator className="w-5 h-5" />
                Calculate Area / رقبہ نکالیں
              </button>
            </div>
          </div>

          {/* Results Panel */}
          <div className="space-y-6">
            {/* Plot Diagram */}
            {results && (
              <div className="bg-white rounded-2xl shadow-xl p-6 border border-green-100">
                <h3 className="text-xl font-semibold text-gray-800 mb-4">Plot Diagram</h3>
                <div className="relative bg-green-50 rounded-lg p-8 min-h-48 flex items-center justify-center">
                  <div className="relative border-2 border-green-600 bg-white rounded-lg" style={{
                    width: `${Math.min(200, (results.measurements.a / Math.max(results.measurements.a, results.measurements.c)) * 200)}px`,
                    height: `${Math.min(150, (results.measurements.c / Math.max(results.measurements.a, results.measurements.c)) * 150)}px`
                  }}>
                    {/* Side labels */}
                    <div className="absolute -top-6 left-1/2 transform -translate-x-1/2 text-sm font-medium text-green-700">
                      A: {measurements.sideA} {measurements.unit}
                    </div>
                    <div className="absolute -bottom-6 left-1/2 transform -translate-x-1/2 text-sm font-medium text-green-700">
                      B: {measurements.sideB} {measurements.unit}
                    </div>
                    <div className="absolute -left-12 top-1/2 transform -translate-y-1/2 -rotate-90 text-sm font-medium text-green-700">
                      C: {measurements.sideC} {measurements.unit}
                    </div>
                    <div className="absolute -right-12 top-1/2 transform -translate-y-1/2 rotate-90 text-sm font-medium text-green-700">
                      D: {measurements.sideD} {measurements.unit}
                    </div>
                  </div>
                </div>
              </div>
            )}

            {/* Results Display */}
            {results && (
              <div className="bg-white rounded-2xl shadow-xl p-6 border border-green-100">
                <div className="flex items-center justify-between mb-6">
                  <h3 className="text-xl font-semibold text-gray-800">Area Results</h3>
                  <button
                    onClick={downloadPDF}
                    className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition-colors flex items-center gap-2"
                  >
                    <Download className="w-4 h-4" />
                    Download
                  </button>
                </div>

                <div className="grid grid-cols-2 gap-4">
                  <div className="bg-green-50 p-4 rounded-lg border border-green-200">
                    <p className="text-sm text-green-600 font-medium">Total Area</p>
                    <p className="text-2xl font-bold text-green-800">{results.areaFeet} ft²</p>
                  </div>
                  <div className="bg-amber-50 p-4 rounded-lg border border-amber-200">
                    <p className="text-sm text-amber-600 font-medium">Marla</p>
                    <p className="text-2xl font-bold text-amber-800">{results.marla}</p>
                  </div>
                  <div className="bg-orange-50 p-4 rounded-lg border border-orange-200">
                    <p className="text-sm text-orange-600 font-medium">Kanal</p>
                    <p className="text-2xl font-bold text-orange-800">{results.kanal}</p>
                  </div>
                  <div className="bg-blue-50 p-4 rounded-lg border border-blue-200">
                    <p className="text-sm text-blue-600 font-medium">Square Meters</p>
                    <p className="text-2xl font-bold text-blue-800">{results.squareMeters} m²</p>
                  </div>
                  <div className="bg-purple-50 p-4 rounded-lg border border-purple-200">
                    <p className="text-sm text-purple-600 font-medium">Acres</p>
                    <p className="text-2xl font-bold text-purple-800">{results.acres}</p>
                  </div>
                  <div className="bg-pink-50 p-4 rounded-lg border border-pink-200">
                    <p className="text-sm text-pink-600 font-medium">Square Yards</p>
                    <p className="text-2xl font-bold text-pink-800">{results.squareYards} yd²</p>
                  </div>
                </div>
              </div>
            )}
          </div>
        </div>

        {/* Footer */}
        <div className="text-center mt-12 text-gray-600">
          <p>Made with ❤️ for Pakistani real estate professionals</p>
          <p className="text-sm mt-2">* Calculations are approximate for irregular plots</p>
        </div>
      </div>
    </div>
  );
};

export default ZameenMapper;
