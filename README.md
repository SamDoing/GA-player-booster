# Facilitador do uso do player
## Atalhos
- Setas direcionais - volume e skip
- X - altera a velocidade
- C - altera a legenda
- F - altera o modo full-screen
- M - altera o modo mini-player
- Espaço - pausa/play
## Misc
Esconde o cursor e a barra de controles quando inativo.
## Como usar
Para utilizar abra o DevTools do navegador, na aba console, copie, cole e execute o codigo abaixo.
- Atalho DevTools Chrome: <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>i</kbd>

Obs: O script só entra em execução após iníciar o vídeo pela primeira vez.
## Código
```
let video;
let buttonPlay;
let buttonFullscreen;
let speedList;
let captionList;
let volumeBar;

let volume = 1.0;
let speedRate = 1.0;
let caption = 'Desativadas';


function updateVol(desiredVol) {
    video.volume = desiredVol;
    volume = desiredVol;
    volumeBar.value = desiredVol * 100;
    volumeBar.style.backgroundSize = desiredVol*100 + '% 100%';
}

function updateSpeed(desiredSpeed) {
    speedList.forEach(speed => {
        if(speed.textContent == desiredSpeed + 'x') 
            speed.click();  
    });
}

function updateCaption() {
    captionList.forEach(cap => {
        if(cap.textContent == caption) 
            cap.click();  
    });
}

const shorts = {
    ArrowRight : function() {
        video.currentTime += 5; 
    },
    ArrowLeft : function() {
        video.currentTime -= 5; 
    },
    ArrowDown : function() {
        let desiredVol = video.volume - 0.05;
        if(desiredVol < 0) desiredVol = 0;
        updateVol(desiredVol);

    },
    ArrowUp : function() {
        let desiredVol = video.volume + 0.05;
        if(desiredVol > 1) desiredVol = 1;

        updateVol(desiredVol);
    },
    space : function() {
        buttonPlay.click();
    },
    c: function() {
        caption = caption == 'Desativadas' ? 'Português':'Desativadas';
        updateCaption();
    },
    x: function() {
        let desiredSpeed = video.playbackRate + 0.5;
        if(desiredSpeed > 2)
            desiredSpeed = 0.5;
        updateSpeed(desiredSpeed);
    },
    f: function() {
        buttonFullscreen.click();
    },
    m: function() {
        document.pictureInPictureElement ?
            document.exitPictureInPicture():video.requestPictureInPicture();
    }
}

function changeVolumeBar() {
    
    let oldVolumeBar = document.querySelectorAll('input')[1];
    volumeBar = oldVolumeBar.cloneNode();
    
    oldVolumeBar.parentNode.appendChild(volumeBar);
    oldVolumeBar.remove();

    volumeBar.oninput = (e) => updateVol(parseFloat(e.target.value/100));
    volumeBar.onchange = (e) => updateVol(parseFloat(e.target.value/100)); 
 
}

function loadApplicationContext() {
    console.log('Loading Application Context');
    
    buttonPlay = document.querySelector('button');
    buttonFullscreen = document.querySelectorAll('button')[8];
    speedList = document.querySelector('.smash-player-menu').firstChild.childNodes;
    captionList = document.querySelectorAll('.smash-player-menu')[1].firstChild.childNodes

    changeVolumeBar();

    let videoContainer = document.querySelector('video').parentNode.parentNode.childNodes[2].firstChild;
    let controls = document.querySelector('video').parentNode.parentNode.childNodes[2].children[1];
    
    if(controls.firstChild.tagName === 'VIDEO')
        controls = document.querySelector('video').parentNode.parentNode.childNodes[2].children[2];
    
        let hiddingTimer;
    
    updateSpeed(speedRate);
    updateCaption();
    updateVol(volume);

    function hideVideoCursorAndControls() {
        videoContainer.style.cursor = 'none';
        controls.style.maxHeight = '0px'; 
    }

    speedList.forEach(s => s.addEventListener('click', (e) =>
        speedRate = parseFloat(e.target.textContent)));

    captionList.forEach(c => c.addEventListener('click', (e) =>
        caption = e.target.textContent));

    videoContainer.addEventListener('mousemove', () => {
        videoContainer.style.cursor = 'auto'
        controls.style.maxHeight = 'none'; 
        
        clearInterval(hiddingTimer);
    
        hiddingTimer = setTimeout(hideVideoCursorAndControls, 2000);
    });

    videoContainer.ondblclick = () => shorts.f();
    
    controls.addEventListener('mouseenter', () => clearInterval(hiddingTimer));

    window.onkeydown = (event) => {
        let keyName = event.key;
    
        if(keyName == ' ')
            keyName = 'space';
    
        if(shorts[keyName]) 
            shorts[keyName]();
    };
}
function setup() {
    video = document.querySelector('video');
    if(!video) return;
    
    video.onplay = () => {
        loadApplicationContext();
        video.onplay = null;
    }
}
const pushState = window.history.pushState;

window.history.pushState = (arg1, arg2) => {
    pushState(arg1, arg2);
    window.onkeydown = null;
    setup();
};

let checkVideo = setInterval(() => {
    if(video) return;
    setup();
}, 500);
```
