# player-de-audio-e-video
tocador web
<!DOCTYPE html>
<html>
	<head>
		<title>Avaliação 2  - Reprodução de video em html 5</title>
		<style type="text/css">
		 body{font-family:Tahoma;}
			span#tempoAtual{background:#fa4b2a; 
			color:#fff;
			border-radius:4px; 
		    box-shadow: 2px 2px 2px #A4A4A4;
		}
		input {background:#009933; 
		    color:#fff;
			border-radius:4px; 
		    box-shadow: 2px 2px 2px #A4A4A4;
		}		
		 button {background:#009933; 
		    color:#fff;
			border-radius:4px; 
		    box-shadow: 2px 2px 2px #A4A4A4;
		}		
		div#video{ 
			width: 800px; 
			background: #323232; 
			margin: 0px auto;
		}		
		div#controls{
			background: #585858;
			padding: 5px;
			color: white;	
		}
		
		video#meuvideo{
			width: 800px; 			
		}
		input#barraTempo{ 
			width: 200px;
		}

		input[type='range']{
			-webkit-appearance: button;
			background: #f0a127;
			border: #666 1px solid;
			height: 5px;
		}
		input[type='range']::-webkit-slider-thumb{
			-webkit-appearance: button;
			background: red;
			height: 15px;
			width: 15px;
			border-radius: 100%;
			cursor: pointer;
		}

		
		</style>
	
	</head>
	<body>
		<!-- Escolhendo o video -->
	
		<div id="video">
		<video id="meuvideo" autoplay>
				<source src="Star Wars The Force Awakens Trailer (Official).mp4" type="video/mp4">
			<!--<track label="English" kind="subtitles" srclang="en" src="/legenda/legenda.vtt" default>-->
				<track src="legenda/StarWars.vtt" kind="subtitles" srclang="en" label="English"><track>
		</video>
		<!-- Define os controles -->
		<div id="controls">
			<div id="progressoBarra"><span id="played" tabindex="2"></span></div>
			<input id="barraTempo" type="range" min="0" max="100" value="0" step="1" />
			<span id="tempoAtual">00:00:00</span> / <span id="tempoTotal">00:00:00</span>
			<button id="playpause" alt="play" title="play" tabindex="1" onclick="PlayPause()" accesskey="p">Play/Pause</button>
			<button id="stop" alt="stop" title="parar" tabindex="3" onclick="stopVideo()" accesskey="s">stop</button>
			<button id="rewind" alt="rewind" title="retroceder" tabindex="4" onclick="MudaVelocidadePlayback('-');" accesskey="r">&laquo;</button>
			<button id="ffwd" alt="fast forward" title="avançar" tabindex="5" onclick="MudaVelocidadePlayback('+');" accesskey="f">&raquo;</button>
			<button id="volumeDown" alt="diminui volume" title="diminui" tabindex="6" onclick="MudaVolume('-')" accesskey="d">-</button>
			<button id="volumeUp" alt="aumenta volume" title="aumenta" tabindex="7" onclick="MudaVolume('+')" accesskey="u">+</button>
			<button id="mute" alt="mute" title="mute" tabindex="8" onclick="Mute()" accesskey="m">mute</button>
		    <button id="fullscreenbtn">[ &nbsp; ]</button>
			Legenda: <button id="btnLegenda" type="button">LIGADO</button>
			

		</div>
		</div>
		<p id="teste"></p>
		</br>
			<div class='statistics'>
        <div class="lead text-center bg-info">
          <h1>Estatisticas de reprodução</h1>

                <tr>
                    <td><span id="infoDuracao">Duração total:</span> </td>
                    <td> <span id="infoTempoDuracao"></span><span class="segundosInfo">segundos</span></td>  </br>  
                </tr>
                 <tr>
                    <td><span id="inforPosicaoAtual">Posição atual:</span></td>
                    <td> <span id="infoTempoAtual"></span><span class="segundosInfo">segundos</span></td> </br>      
                </tr>
                  <tr>
                    <td><span id="infoPosicaoPausa">Tempos em pausa: </span></td>
                    <td> <span id="infoTempoPausa"></span></td>       
                </tr>
             

             

                
            </table>
			<div id="pausas"></div>
</div>


		
		<script>		
		
		
		 
			// identificando o objeto de vídeo
			var video = document.getElementsByTagName("video")[0];
			
			var fullscreenbtn = document.getElementById('fullscreenbtn');
			
			var btnLegenda = document.getElementById("btnLegenda");	
			// desliga os controles default do navegador
			video.controls = false;
			// identificando o objeto botão play/pause 
			var ppbutton = document.getElementById("playpause");
							
			var tempoAtual, tempoTotal, tempoVideoAtualizado;
			btnLegenda.innerHTML = "DESLIGADO";
			
            barraTempo.addEventListener("change", progressoBarra, false);
			btnLegenda.addEventListener("click", MostraLegenda, false);
			video.addEventListener("timeupdate", barraTempoAtualizacao, false);	
			// atualizando o video
            barraTempo = document.getElementById("barraTempo");
			 
			tempoAtual = document.getElementById("tempoAtual");
			//identificando o tempo em que se encontra o video
			tempoTotal = document.getElementById("tempoTotal");				
			// tempo total do video
			
			// Adcionando eventlistener para mudar o valor entre play/pause quando o botão é acionado
			video.addEventListener('play', function() {
				ppbutton.title = "pause";
				ppbutton.innerHTML = "pause";
			}, false);
			video.addEventListener('pause', function() {
				ppbutton.title = "play";
				ppbutton.innerHTML = "play";
			}, false);
			// Adicionando um eventlistener na barra de progresso para atualiza-la
			//video.addEventListener('timeupdate', updateProgress, false);
			
			// Adicionar eventlistener para pausar o vídeo (que classifica o botão play / pause) quando termina o vídeo
			video.addEventListener('ended', function() { this.pause(); }, false);
			fullscreenbtn.addEventListener("click", toggleFullScreen, false);
			
			// para o video e reseta o tempo para 0
			function stopVideo() {
				video.pause();
				video.currentTime = 0;
				video.playbackRate= 1;
				zera();
		        limpa();
			}
			
			// Alterna o botão play / pause entre reproduzir e pausar
			function PlayPause() {
				if (video.paused || video.ended) {
					if (video.ended) video.currentTime = 0;
					video.play();
				    para();
					tempoPausa();
				}
				else {
					video.pause();
				   	 inicia();
					
				}
			}
			
			// controles de volume do video
			function MudaVolume(direction) {
				var volume = Math.floor(video.volume * 10) / 10;
				video.muted = false;
				if (direction == "-") {
					if (volume <= 0.1) video.volume = 0;
					else video.volume -= 0.1;
				}
				else {
					if (volume >= 0.9) video.volume = 1;
					else video.volume += 0.1;   
				}
			}
			
			// Alterna o valor mute do vídeo
			function Mute() {
				var mute = document.getElementById("mute");
				if (video.muted) {
					mute.innerHTML = "mute";
					video.muted = false;
				}
				else {
					mute.innerHTML = "unmute";
					video.muted = true;
				}
			}
			
			// Função Mostrar Legenda
			function MostraLegenda(){
			if (btnLegenda.innerHTML == "LIGADO"){
				btnLegenda.innerHTML = "DESLIGADO";
				legenda();
			}else{
				btnLegenda.innerHTML = "LIGADO";
				legenda();
			}				
		}
		
		
	
			// Alterando a velocidade de reprodução do video
			function MudaVelocidadePlayback(direction) {
				if (video.playbackRate != undefined) {
					if (direction == "-") video.playbackRate -= 1;
					else video.playbackRate += 1;
					//document.getElementById("teste").innerHTML=formatatempo(video.currentTime) + "<br>";
				}
				else {
					if (direction == "-") video.currentTime -= 1;
					else video.currentTime += 1;
				   //document.getElementById("teste").innerHTML=formatatempo(video.currentTime) + "<br>;
				}
			}
			function toggleFullScreen() {
			if (video.requestFullScreen) {
				vid.requestFullScreen();

			} else if (video.webkitRequestFullScreen) {
				video.webkitRequestFullScreen();

			} else if (video.mozRequestFullScreen) {
				video.mozRequestFullScreen();
			};
		}
		

	//video.addEventListener("timeupdate", function() { 
	 // function tempoPausa() { 
      //var TimeElapsed = document.getElementById('infoTempoPausa'); 
     // var seconds = parseInt(video.currentTime % 60); 
      //var minutes = parseInt((video.currentTime / 60) % 60); 
     // TimeElapsed.innerHTML = minutes + '.' + seconds + 'sec'; 
    //} 
	
	
	///ativa e desativa legenda
		var Subtitle;
		function legenda(){
        if(!Subtitle){
            video.textTracks[0].mode= "showing";
            console.log( video.textTracks)
        }else{
            video.textTracks[0].mode= "disabled";
        }
        	Subtitle = !Subtitle;
    	};
		
		
		
			function progressoBarra(){
			video.currentTime = video.duration * (barraTempo.value / 100);
		    }
			
			function barraTempoAtualizacao(){
			var hora = "00:";
			var novoTempo = video.currentTime * (100 / video.duration);
			barraTempo.value = novoTempo;
			
			var minAtual = Math.floor(video.currentTime / 60);
			var segAtual = Math.floor(video.currentTime - minAtual * 60);
			var minDuracao = Math.floor(video.duration / 60);
			var segDuracao = Math.floor(video.duration - minDuracao * 60);
			if (segAtual < 10){segAtual = "0" + segAtual;}
			if (segDuracao < 10){segDuracao = "0" + segDuracao;}
			if (minAtual < 10){minAtual = "0" + minAtual;}
			if (minDuracao < 10){minDuracao = "0" + minDuracao;}
			 
			tempoAtual.innerHTML = hora + minAtual + ":" + segAtual;
			tempoTotal.innerHTML = hora + minDuracao + ":" + segDuracao;
			
						
		}
		
function formatatempo(segs) {
min = 0;
hr = 0;

while(segs>=60) {
if (segs >=60) {
segs = segs-60;
min = min+1;
}
}

while(min>=60) {
if (min >=60) {
min = min-60;
hr = hr+1;
}
}

if (hr < 10) {hr = "0"+hr}
if (min < 10) {min = "0"+min}
if (segs < 10) {segs = "0"+segs}
fin = hr+":"+min+":"+segs
return fin;
}
var segundos = 0; //inicio do cronometro
function conta() {
segundos++;
document.getElementById("infoTempoPausa").innerHTML = formatatempo(segundos);
}

function inicia(){
interval = setInterval("conta();",1000);
}

function para(){
clearInterval(interval);
}

function zera(){
clearInterval(interval);
segundos = 0;
document.getElementById("infoTempoPausa").innerHTML = formatatempo(segundos);
}
function tempopausa() {

document.getElementById("infoTempoPausa").innerHTML=formatatempo(segundos) + "<br>";
}
function limpa() {
	document.getElementById("infoTempoPausa").innerHTML = formatatempo(segundos)="";
}
		</script>
	</body>
</html>
