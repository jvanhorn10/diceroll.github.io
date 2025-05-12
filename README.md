<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nitrogen Cycle Digital Dice</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/18.2.0/umd/react.production.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react-dom/18.2.0/umd/react-dom.production.min.js"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.2.19/tailwind.min.css" rel="stylesheet">
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f8ff;
            padding: 20px;
        }
        .dice-container {
            max-width: 600px;
            margin: 0 auto;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            padding: 20px;
        }
        .dice {
            width: 80px;
            height: 80px;
            background-color: white;
            border: 2px solid #ccc;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 36px;
            font-weight: bold;
            color: #3182ce;
            margin: 20px auto;
        }
        .result-box {
            background-color: #f9fafb;
            border: 1px solid #e5e7eb;
            border-radius: 8px;
            padding: 15px;
            margin-top: 15px;
        }
        .next-info {
            border-top: 1px solid #e5e7eb;
            padding-top: 10px;
            margin-top: 10px;
        }
        h1 {
            color: #1e40af;
            text-align: center;
            margin-bottom: 20px;
        }
        .description {
            text-align: center;
            margin-bottom: 20px;
            color: #4b5563;
        }
        select, button {
            width: 100%;
            padding: 10px;
            margin-bottom: 15px;
            border-radius: 5px;
        }
        button {
            background-color: #3182ce;
            color: white;
            font-weight: bold;
            border: none;
            cursor: pointer;
            transition: background-color 0.2s;
        }
        button:hover {
            background-color: #2c5282;
        }
        button:disabled {
            background-color: #9ca3af;
            cursor: not-allowed;
        }
        .station-info {
            background-color: #e0f2fe;
            border-radius: 8px;
            padding: 10px;
            margin: 20px 0;
        }
    </style>
</head>
<body>
    <div class="dice-container">
        <h1>Nitrogen Cycle Digital Dice</h1>
        <p class="description">Roll the dice to determine the journey of your nitrogen atom through the nitrogen cycle.</p>
        
        <div id="dice-roller-app"></div>
        
        <div class="station-info">
            <h2 class="text-lg font-bold text-blue-800 mb-2">How to use this tool:</h2>
            <ol class="list-decimal pl-5 space-y-1">
                <li>Select your current station from the dropdown</li>
                <li>Click "Roll the Dice!"</li>
                <li>Read what happens to your nitrogen atom</li>
                <li>Record the information in your Nitrogen Cycle Journey form</li>
                <li>For your next roll, select your new location (where the nitrogen atom moved to)</li>
            </ol>
        </div>
    </div>

    <script type="text/javascript">
        const { useState, useEffect } = React;

        function DiceRoller() {
            const [diceResult, setDiceResult] = useState(null);
            const [isRolling, setIsRolling] = useState(false);
            const [currentStation, setCurrentStation] = useState('');
            const [nextDestination, setNextDestination] = useState('');
            const [nitrogenForm, setNitrogenForm] = useState('');
            const [whatHappened, setWhatHappened] = useState('');

            const stations = [
                { name: "Atmosphere", value: "atmosphere" },
                { name: "Lake", value: "lake" },
                { name: "Rain", value: "rain" },
                { name: "Fertilizers", value: "fertilizers" },
                { name: "Soil", value: "soil" },
                { name: "Ocean", value: "ocean" },
                { name: "Live Animals", value: "live-animals" },
                { name: "Animal Waste", value: "animal-waste" },
                { name: "Dead Plants and Animals", value: "dead-plants-animals" },
                { name: "Live Plants", value: "live-plants" },
                { name: "Humans", value: "humans" }
            ];

            const stationResults = {
                atmosphere: [
                    { roll: 1, result: "A storm rolls in and lightning strikes! N₂ in the atmosphere combines with oxygen and becomes N₂O. It starts to rain and you end up in the soil.", destination: "Soil", form: "N₂O" },
                    { roll: 2, result: "Blue green algae and bacteria convert you into NH₃ for seaweed, bringing you into the ocean.", destination: "Ocean", form: "NH₃" },
                    { roll: [3, 4, 5], result: "Humans extract N₂ from the air.", destination: "Humans", form: "N₂" },
                    { roll: 6, result: "Bacteria extract you from the air and bring you to the soil in the form of NO₃.", destination: "Soil", form: "NO₃" }
                ],
                lake: [
                    { roll: [1, 2, 3], result: "You are NO₃ which plants need to live. You are absorbed by the roots and now are within a live plant.", destination: "Live Plants", form: "NO₃" },
                    { roll: [4, 5, 6], result: "You travel through a connecting river and eventually make it into the ocean as NO₃.", destination: "Ocean", form: "NO₃" }
                ],
                rain: [
                    { roll: [1, 2], result: "You fall into a lake as NO₃.", destination: "Lake", form: "NO₃" },
                    { roll: [3, 4], result: "You fall on land and become part of the soil as NO₃.", destination: "Soil", form: "NO₃" },
                    { roll: [5, 6], result: "You fall into the ocean as NH₃.", destination: "Ocean", form: "NH₃" }
                ],
                fertilizers: [
                    { roll: [1, 2], result: "You dissolve into NO₃ and wash into a lake.", destination: "Lake", form: "NO₃" },
                    { roll: [3, 4], result: "You are NO₃, the exact form of nitrogen that plants need to live. A flower's roots absorb you and you are now within a live plant.", destination: "Live Plants", form: "NO₃" },
                    { roll: [5, 6], result: "You are released as N₂O in the process of denitrification and end up in the atmosphere.", destination: "Atmosphere", form: "N₂O" }
                ],
                soil: [
                    { roll: [1, 2], result: "You become part of a protein and wash into a lake.", destination: "Lake", form: "Protein" },
                    { roll: [3, 4], result: "You are NO₃, the exact form of nitrogen that plants need to live. A flower's roots absorb you and you are now within a live plant.", destination: "Live Plants", form: "NO₃" },
                    { roll: [5, 6], result: "Bacteria have transformed you into nitrogen gas (N₂) and you are now part of the atmosphere.", destination: "Atmosphere", form: "N₂" }
                ],
                ocean: [
                    { roll: 1, result: "A big storm washes you inside of a wave onto land and you flow into a small lake.", destination: "Lake", form: "NO₃" },
                    { roll: [2, 3], result: "You are NO₃, the exact form of nitrogen that seaweed needs to live. The seaweed absorbs you and you are now within a live plant.", destination: "Live Plants", form: "NO₃" },
                    { roll: [4, 5, 6], result: "Bacteria have transformed you into nitrogen gas (N₂) and you are now part of the atmosphere.", destination: "Atmosphere", form: "N₂" }
                ],
                "live-animals": [
                    { roll: [1, 2, 3], result: "The animal that you are within has died. Go to dead plants and animals as NH₄.", destination: "Dead Plants and Animals", form: "NH₄" },
                    { roll: [4, 5, 6], result: "The animal you are within has peed or pooped. You are now in animal waste as urea.", destination: "Animal Waste", form: "Urea" }
                ],
                "animal-waste": [
                    { roll: [1, 2], result: "Bacteria and fungi break you down into NH₄. You have decomposed into the soil.", destination: "Soil", form: "NH₄" },
                    { roll: [3, 4], result: "A farming supply company has scooped you up and put you into fertilizer as NH₃.", destination: "Fertilizers", form: "NH₃" },
                    { roll: [5, 6], result: "It rains and you dissolve into the runoff as NH₄ and flow until you reach a lake.", destination: "Lake", form: "NH₄" }
                ],
                "dead-plants-animals": [
                    { roll: [1, 2], result: "Bacteria and fungi break you down and you decom
