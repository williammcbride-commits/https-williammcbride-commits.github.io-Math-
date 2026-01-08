<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Math AI Tutor</title>

<!-- Google Font: Cavnet -->
<link href="https://fonts.googleapis.com/css2?family=Cavnet&display=swap" rel="stylesheet">

<style>
/* ------------------- Page & Theme ------------------- */
body {
  font-family: Arial, sans-serif;
  margin: 0;
  background: linear-gradient(to bottom right, #1b5e20, #fdd835);
  color: #fff;
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 100vh;
}

header {
  font-family: 'Cavnet', cursive;
  background: rgba(27,94,32,0.9);
  color: #ffd700;
  width: 100%;
  padding: 20px;
  text-align: center;
  box-shadow: 0 4px 6px rgba(0,0,0,0.3);
}

header h1 {
  margin: 0;
  font-size: 2.5em;
}

header p {
  margin: 5px 0 0;
  font-size: 1.2em;
}

/* ------------------- Chat Box ------------------- */
main {
  flex: 1;
  width: 90%;
  max-width: 700px;
  display: flex;
  flex-direction: column;
  margin-top: 20px;
}

.chat-box {
  font-family: 'Cavnet', cursive;
  background: rgba(0,0,0,0.1);
  flex: 1;
  border-radius: 15px;
  padding: 15px;
  overflow-y: auto;
  margin-bottom: 10px;
  border: 2px solid #ffd700;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

/* ------------------- Chat Bubbles ------------------- */
.message {
  max-width: 75%;
  padding: 10px 15px;
  border-radius: 20px;
  line-height: 1.4;
  word-wrap: break-word;
}

.user {
  background-color: #ffd700;
  color: #1b5e20;
  align-self: flex-end;
  border-bottom-right-radius: 0;
}

.ai {
  background-color: #a5d6a7;
  color: #1b5e20;
  align-self: flex-start;
  border-bottom-left-radius: 0;
}

.highlight {
  background-color: #fff59d;
  font-weight: bold;
  padding: 2px 4px;
  border-radius: 3px;
}

strong {
  color: #1b5e20;
}

/* ------------------- Input ------------------- */
.input-container {
  display: flex;
  gap: 10px;
}

input[type="text"] {
  flex: 1;
  padding: 10px;
  border-radius: 5px;
  border: 2px solid #ffd700;
  font-size: 1em;
}

button {
  padding: 10px 15px;
  border-radius: 5px;
  border: none;
  font-weight: bold;
  cursor: pointer;
}

#sendBtn {
  background: #1b5e20;
  color: #ffd700;
}

#sendBtn:hover {
  background: #2e7d32;
}

#clearBtn {
  background: #ffd700;
  color: #1b5e20;
}

#clearBtn:hover {
  background: #ffeb3b;
}
</style>

<!-- Safety: Content Security Policy -->
<meta http-equiv="Content-Security-Policy" content="
  default-src 'self';
  script-src 'self';
  style-src 'self';
  object-src 'none';
">
</head>
<body>

<header>
  <h1>🧠 Math AI Tutor</h1>
  <p>6th • 7th • 8th Grade Math Help — Calm, step-by-step explanations</p>
</header>

<main>
  <div id="chat" class="chat-box"></div>

  <div class="input-container">
    <input type="text" id="input" placeholder="Ask me about fractions, ratios, equations..." />
    <button id="sendBtn">Send</button>
    <button id="clearBtn">Clear</button>
  </div>
</main>

<script>
// ------------------ Math Topics ------------------
const mathTopics = {
fractions:{grade:"6th Grade",category:"Rational Numbers",keyIdea:"Parts of a whole",simpleExplanation:"Fractions show how many equal parts you have.",steps:["Bottom number = total equal parts","Top number = parts you have","Keep parts the same size"],commonMistakes:["Adding bottom numbers","Unequal parts"],calmingTip:"Draw a picture if your brain feels stuck."},
decimals:{grade:"6th Grade",category:"Rational Numbers",keyIdea:"Parts using place value",simpleExplanation:"Decimals are fractions written using place value.",steps:["Line up decimals","Add zeros if needed","Move decimal carefully"],commonMistakes:["Not lining up decimals","Moving decimal the wrong way"],calmingTip:"Slow down when moving the decimal."},
integers:{grade:"6th Grade",category:"Rational Numbers",keyIdea:"Positive and negative numbers",simpleExplanation:"Integers include positive numbers, negative numbers, and zero.",steps:["Find zero on the number line","Move right for positive","Move left for negative"],commonMistakes:["Forgetting direction","Mixing signs"],calmingTip:"Use a number line to stay calm."},
absolutevalue:{grade:"6th Grade",category:"Rational Numbers",keyIdea:"Distance from zero",simpleExplanation:"Absolute value tells how far a number is from zero.",steps:["Ignore the sign","Write the positive value"],commonMistakes:["Keeping the negative sign"],calmingTip:"Distance is never negative."},
ratios:{grade:"6th Grade",category:"Ratios",keyIdea:"Comparing amounts",simpleExplanation:"Ratios compare two quantities.",steps:["Write what is being compared","Keep units the same","Simplify if possible"],commonMistakes:["Mixing units","Not simplifying"],calmingTip:"Write labels to reduce stress."},
proportions:{grade:"7th Grade",category:"Ratios",keyIdea:"Two ratios that are equal",simpleExplanation:"Proportions show two equal ratios.",steps:["Set ratios equal","Cross multiply","Solve"],commonMistakes:["Cross multiplying wrong numbers"],calmingTip:"Draw an X for cross multiplication."},
percentages:{grade:"7th Grade",category:"Percents",keyIdea:"Out of 100",simpleExplanation:"Percents show how much out of 100.",steps:["Change percent to decimal","Multiply","Label answer"],commonMistakes:["Decimal in wrong place"],calmingTip:"Say the decimal move out loud."},
expressions:{grade:"7th Grade",category:"Algebra",keyIdea:"Math without equals",simpleExplanation:"Expressions combine numbers and variables.",steps:["Combine like terms","Follow order of operations"],commonMistakes:["Combining unlike terms"],calmingTip:"Circle like terms first."},
linearequations:{grade:"7th–8th Grade",category:"Algebra",keyIdea:"Solve for the variable",simpleExplanation:"Linear equations find the value of the variable.",steps:["Circle the variable","Undo addition or subtraction","Undo multiplication or division","Check your answer"],commonMistakes:["Skipping steps","Forgetting to check"],calmingTip:"Always solve in the same order."},
inequalities:{grade:"7th–8th Grade",category:"Algebra",keyIdea:"Greater or less than",simpleExplanation:"Inequalities compare values instead of making them equal.",steps:["Solve like an equation","Flip sign if multiplying/dividing by negative","Graph solution"],commonMistakes:["Forgetting to flip sign"],calmingTip:"Pause when you see a negative."},
graphing:{grade:"8th Grade",category:"Graphing",keyIdea:"Showing math visually",simpleExplanation:"Graphs show relationships using points and lines.",steps:["Label x and y axes","Plot points","Connect carefully"],commonMistakes:["Swapping x and y"],calmingTip:"Plot one point at a time."},
area:{grade:"6th–7th Grade",category:"Geometry",keyIdea:"Space inside",simpleExplanation:"Area measures the space inside a shape.",steps:["Choose the right formula","Plug in numbers","Write units"],commonMistakes:["Wrong formula","Missing units"],calmingTip:"Write the formula first."},
volume:{grade:"6th–7th Grade",category:"Geometry",keyIdea:"Space inside 3D shapes",simpleExplanation:"Volume measures how much space a 3D object holds.",steps:["Use length × width × height","Multiply carefully","Write cubic units"],commonMistakes:["Missing one dimension"],calmingTip:"Touch each dimension as you multiply."},
pythagorean:{grade:"8th Grade",category:"Geometry",keyIdea:"Right triangle rule",simpleExplanation:"a² + b² = c² helps find missing sides.",steps:["Label hypotenuse","Square the legs","Add","Square root"],commonMistakes:["Wrong side labeled c"],calmingTip:"Label before solving."}
};

// ------------------ Safety & Input ------------------
function sanitizeInput(text){
  return text.replace(/</g,"&lt;").replace(/>/g,"&gt;").replace(/"/g,"&quot;").replace(/'/g,"&#039;");
}

const MAX_INPUT_LENGTH = 200;

// ------------------ Highlight ------------------
function highlight(text){
  return text.replace(/(fraction|decimal|integer|absolutevalue|ratio|proportion|percent|expression|linearequation|inequality|graph|area|volume|pythagorean|steps|mistakes|calm|grade)/gi,'<span class="highlight">$1</span>');
}

// ------------------ Chat ------------------
function addMessage(sender,text,type){
  const p=document.createElement("p");
  p.classList.add("message");
  p.classList.add(type);
  p.innerHTML=`<strong>${sanitizeInput(sender)}:</strong> ${highlight(sanitizeInput(text))}`;
  chat.appendChild(p);
  chat.scrollTop=chat.scrollHeight;
}

function respond(query){
  const q=query.toLowerCase();
  for(const key in mathTopics){
    if(q.includes(key)){
      const m=mathTopics[key];
      return `
${key.toUpperCase()} (${m.grade})
📘 ${m.keyIdea}
🧠 ${m.simpleExplanation}

🪜 Steps:
- ${m.steps.join("\n- ")}

⚠️ Common mistakes:
- ${m.commonMistakes.join("\n- ")}

🧘 Calm Tip:
${m.calmingTip}
      `;
    }
  }
  return "You can ask about fractions, decimals, integers, absolute value, ratios, proportions, percentages, expressions, linear equations, inequalities, graphing, area, volume, or the Pythagorean theorem.";
}

// ------------------ Interaction ------------------
async function talk(){
  let text=input.value.trim();
  if(!text) return;
  if(text.length>MAX_INPUT_LENGTH){
    addMessage("Math AI Tutor","⚠️ Please keep your question under 200 characters.","ai");
    input.value="";
    return;
  }
  text=sanitizeInput(text);
  addMessage("You",text,"user");
  const aiResponse=respond(text);
  addMessage("Math AI Tutor",aiResponse,"ai");
  input.value="";
}

function clearChat(){
  chat.innerHTML="";
  addMessage("Math AI Tutor","Chat cleared. Ask me math questions in simple, calm steps.","ai");
}

// ------------------ Event Listeners ------------------
sendBtn.onclick=talk;
input.onkeypress=e=>{if(e.key==="Enter") talk();};
clearBtn.onclick=clearChat;

// ------------------ Initial Message ------------------
addMessage("Math AI Tutor","Hi! I help with middle school math. Ask me about fractions, ratios, equations, geometry, or anything from 6th to 8th grade. I give calm, step-by-step explanations.","ai");
</script>

</body>
</html>
