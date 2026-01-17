# etea17practice
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>ETEA Practice Test</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">

<style>
body{
  font-family:'Poppins',sans-serif;
  background:linear-gradient(135deg,#ff9a9e,#fecfef);
  margin:0;
}

.container{
  max-width:900px;
  margin:20px auto;
  background:#fff;
  padding:30px;
  border-radius:20px;
  box-shadow:0 10px 30px rgba(0,0,0,.2);
}

h2{text-align:center}
.center{text-align:center}

input{
  width:100%;
  padding:12px;
  margin-bottom:15px;
  border-radius:10px;
  border:2px solid #ddd;
}

button{
  padding:12px;
  border:none;
  border-radius:10px;
  font-size:16px;
  font-weight:bold;
  cursor:pointer;
  margin:5px;
  min-height:44px;
}

#startBtn{background:#2980b9;color:#fff;width:100%}

#timer{
  background:#e74c3c;
  color:#fff;
  padding:15px;
  text-align:center;
  border-radius:10px;
  font-size:18px;
}

.option{
  border:2px solid #ddd;
  padding:15px;
  border-radius:10px;
  margin-bottom:12px;
  cursor:pointer;
}

.option.correct{background:#2980b9;color:#fff}
.option.wrong{background:#e74c3c;color:#fff}

.buttons{
  display:flex;
  justify-content:space-between;
}

#submitBtn{
  display:none;
  background:#27ae60;
  color:white;
  width:100%;
}

.subject-box{
  background:#f4f6f7;
  padding:12px;
  border-radius:10px;
  margin:8px 0;
}

.review-option.correct{
  background:#2980b9;
  color:#fff;
  padding:8px;
  border-radius:8px;
  margin:4px 0;
}

.review-option.wrong{
  background:#e74c3c;
  color:#fff;
  padding:8px;
  border-radius:8px;
  margin:4px 0;
}

.footer{
  text-align:center;
  color:#555;
  margin:20px 0;
  font-size:14px;
}

/* MOBILE ONLY */
@media(max-width:768px){
  .container{margin:10px;padding:18px;border-radius:14px}
  .buttons{flex-direction:column;gap:8px}
  button{width:100%;font-size:15px}
  .option{font-size:15px;padding:12px}
}
</style>
</head>

<body>

<!-- LOGIN -->
<div class="container center" id="loginDiv">
<h2>ETEA Practice Test</h2>
<input id="studentName" placeholder="Student Name">
<input id="rollNo" placeholder="Roll Number">
<button id="startBtn" onclick="startCountdown()">Start Test</button>
<div id="countdown" style="font-size:28px;color:red"></div>
</div>

<!-- QUIZ -->
<div class="container" id="quizDiv" style="display:none">
<div id="timer">Time Left: 60:00</div>
<div id="questionBox"></div>
<div id="optionsBox"></div>
<div class="buttons">
<button onclick="prevQ()">Previous</button>
<button onclick="skipQ()">Skip</button>
<button onclick="nextQ()" id="nextBtn" disabled>Next</button>
</div>
<button id="submitBtn" onclick="submitTest()">Submit Test</button>
</div>

<!-- RESULT -->
<div class="container center" id="resultDiv" style="display:none">
<h2 id="resStatus"></h2>
<p id="resScore"></p>
<p id="resTime"></p>
<h3>Subject-Wise Result</h3>
<div id="subjectResult"></div>
<button onclick="showReview()">Review Test</button>
<button onclick="location.reload()">Restart</button>
</div>

<!-- REVIEW -->
<div class="container" id="reviewDiv" style="display:none">
<h2 class="center">Test Review</h2>
<div id="reviewContent"></div>
<button onclick="backToResult()">Back to Result</button>
</div>

<div class="footer">
Created by <b>MUHAMMAD FAIZAN NAWAB</b>
</div>

<script>
const questions=[
{subject:"Biology",q:"The process by which pollen grains reach the stigma is known as:",o:["Fertilization","Germination","Pollination","Reproduction"],c:2},
{subject:"Biology",q:"Which part of the flower develops into fruit after fertilization?",o:["Ovule","Ovary","Stigma","Style"],c:1},
{subject:"Mathematics",q:"Simplify the ratio 18 : 24",o:["2 : 3","3 : 4","4 : 3","6 : 8"],c:0},
{subject:"Mathematics",q:"40% of 200 is:",o:["60","70","80","90"],c:2},
{subject:"English",q:"The book is ___ the table.",o:["in","on","at","under"],c:1},
{subject:"English",q:"By next year, he ___ ten books.",o:["writes","wrote","will have written","has written"],c:2}
];

let answers=new Array(questions.length).fill(null);
let index=0,time=3600,startTime,timerInt;

function startCountdown(){
if(!studentName.value||!rollNo.value){alert("Fill all fields");return;}
let c=3;countdown.innerText=c;
let i=setInterval(()=>{
c--;countdown.innerText=c;
if(c===0){
clearInterval(i);
loginDiv.style.display="none";
quizDiv.style.display="block";
startTime=Date.now();
loadQ();startTimer();
}},1000);
}

function loadQ(){
let q=questions[index];
questionBox.innerHTML=`<h3>${index+1}. (${q.subject}) ${q.q}</h3>`;
optionsBox.innerHTML="";
q.o.forEach((op,i)=>{
let d=document.createElement("div");
d.className="option";
d.innerText=op;
d.onclick=()=>{
if(answers[index]!=null)return;
answers[index]=i;
d.classList.add(i===q.c?"correct":"wrong");
nextBtn.disabled=false;
};
optionsBox.appendChild(d);
});
nextBtn.disabled=answers[index]==null;
submitBtn.style.display=index===questions.length-1?"block":"none";
}

function nextQ(){if(index<questions.length-1){index++;loadQ();}}
function prevQ(){if(index>0){index--;loadQ();}}
function skipQ(){index=(index+1)%questions.length;loadQ();}

function startTimer(){
timerInt=setInterval(()=>{
let m=Math.floor(time/60),s=time%60;
timer.innerText=`Time Left: ${m}:${s<10?'0':''}${s}`;
if(time--<=0){clearInterval(timerInt);submitTest();}
},1000);
}

function submitTest(){
clearInterval(timerInt);
quizDiv.style.display="none";
resultDiv.style.display="block";

let score=0,stats={};
questions.forEach((q,i)=>{
if(!stats[q.subject])stats[q.subject]={t:0,c:0};
stats[q.subject].t++;
if(answers[i]===q.c){score++;stats[q.subject].c++;}
});

resStatus.innerText=(score/questions.length*100)>=50?"PASSED":"FAILED";
resScore.innerText=`Score: ${score}/${questions.length}`;
resTime.innerText=`Time Taken: ${Math.floor((Date.now()-startTime)/60000)} minutes`;

subjectResult.innerHTML="";
for(let s in stats){
subjectResult.innerHTML+=`<div class="subject-box">${s}: ${stats[s].c}/${stats[s].t}</div>`;
}
}

function showReview(){
resultDiv.style.display="none";
reviewDiv.style.display="block";
reviewContent.innerHTML="";
questions.forEach((q,i)=>{
let h=`<h4>${i+1}. ${q.q}</h4>`;
q.o.forEach((op,idx)=>{
let cls="review-option";
if(idx===q.c)cls+=" correct";
else if(answers[i]===idx)cls+=" wrong";
h+=`<div class="${cls}">${op}</div>`;
});
reviewContent.innerHTML+=h;
});
}

function backToResult(){
reviewDiv.style.display="none";
resultDiv.style.display="block";
}
</script>

</body>
</html>
