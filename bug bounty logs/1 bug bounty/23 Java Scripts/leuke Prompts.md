in Chrome /inspect / console
eerst: allow pasting
in edge / inspect / top(links boven)
const naam = prompt("Wat is je naam?");

alert(`Hallo ${naam}`);
    1.Leeftijd berekenen
const leeftijd = prompt("Hoe oud ben je?");
alert(`Over 10 jaar ben je ${Number(leeftijd) + 10} jaar!`);
😂 2. Grappige reactie
js
Code kopiëren
const eten = prompt("Wat is je favoriete eten?");
alert(`${eten}? Dat eet ik dus ALTIJD op dinsdag!`);
🔢 3. Kleine rekenmachine
js
Code kopiëren
const a = Number(prompt("Getal 1:"));
const b = Number(prompt("Getal 2:"));
alert(`Som = ${a + b}`);
💡 4. Raadspelletje (geheime code)
js
Code kopiëren
const geheim = 7;
const gok = Number(prompt("Raad het getal tussen 1 en 10"));

if(gok === geheim){
    alert("🎉 Correct!");
} else {
    alert("Fout! Het was " + geheim);
}
🐶 5. Emoji-generator
js
Code kopiëren
const dier = prompt("Noem een dier:");
alert(`Hier is een cadeau: ${dier} 🐾🐾🐾`);
😎 6. Naam-omkeerder
js
Code kopiëren
const naam = prompt("Wat is je naam?");
alert(`Achterstevoren: ${naam.split("").reverse().join("")}`);
🔮 7. Mini Magic 8-ball
js
Code kopiëren
const vraag = prompt("Stel een vraag:");
const antwoorden = ["Ja!", "Nee!", "Misschien...", "ZEKER weten!", "Twijfelachtig."];
alert(antwoorden[Math.floor(Math.random() * antwoorden.length)]);
🚀 8. Ruimte-reis berekening
js
Code kopiëren
const km = Number(prompt("Hoeveel kilometer moet je reizen naar Mars?"));
alert(`Als je 1000 km/u reist, doe je er ${km / 1000} uur over!`);
📛 9. Naam kleur-keuze
js
Code kopiëren
const naam = prompt("Wat is je naam?");
const kleur = prompt("Favoriete kleur?");
alert(`${naam} houdt van ${kleur}!`);
🤖 10. Robot-stem simulatie
js
Code kopiëren
const tekst = prompt("Typ iets dat de robot moet zeggen:");
alert(`🤖 BEEP BOOP: ${tekst.toUpperCase()}`);
