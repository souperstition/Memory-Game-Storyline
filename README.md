# Memory Game

💻 [Live Site Demo](https://itsdani.me/sl/memorygame/story.html)

## :bookmark_tabs: Table of Contents

- [Project Overview](#-project-overview-top)
- [Purpose](#chart_with_upwards_trend-purpose-top)
- [Process](#triangular_ruler-process-top)
- [Variables](#warning-variables-top)
- [Triggers](#boom-triggers-top)
- [Custom Javascript](#keyboard-custom-javascript-top)
- [Results](#partying_face-results-top)

## 🔎 Project Overview [:top:](#bookmark_tabs-table-of-contents)

This is a memory game created in Articulate Storyline 3, but unlike most memory games, the matching pairs are not identical. Instead, the purpose is to match the flute fingerings to their equivalent notes on the treble clef.

The cards are designed to animate when flipped over. Once a user has clicked two cards, the game will test for a match. 

| If a match has been found, the cards remain face up and in a greyed-out "disabled" state. |
| --- |
| ![match](https://github.com/souperstition/Memory-Game-Storyline/blob/main/img/match.gif?raw=true) |
| If no match was found, they flip back over and the game continues. |
| ![no match](https://github.com/souperstition/Memory-Game-Storyline/blob/main/img/no-match.gif?raw=true) |

In order to ensure a different game every time the page is visited, the cards shuffle on each slide load. 

## :chart_with_upwards_trend: Purpose [:top:](#bookmark_tabs-table-of-contents)

Last year, a flute student of mine was feeling discouraged and unmotivated. Her progress had stalled slightly, and her mood during our lessons had dropped. I was concerned, so I spoke to her about it. We took some time to reassess her goals and compare them with where she is now. I asked her what she thought might be keeping her from reaching the goals she'd expressed.

Her answer: flute fingerings. She often had to stop and look up a fingering while she was practicing, which is very frustrating for a beginner. She just wanted to play! She didn't have a lot of time to practice outside of our lessons, so I really needed a solution that would make the most of our time and help her to nail down those memories. I came up with the idea of creating a matching interaction for her that we could play whenever she started to appear frustrated and in need of a break.

## :triangular_ruler: Process [:top:](#bookmark_tabs-table-of-contents)

I needed to include more notes than I could use in a single game, so I created three scenes: one for each version of the game. 

The first scene is just a blank slide that automatically skips to the next slide:

| An empty slide at the start allows you to skip the play button |
| --- |
| ![empty slide](https://github.com/souperstition/Memory-Game-Storyline/blob/main/img/empty-slide.png?raw=true) |

## :warning: Variables [:top:](#bookmark_tabs-table-of-contents)

There is a variable for each note (A-G). They should be of type number, but you can see my slipup didn't affect the results. A value of 0 means its cards are still in play; 1 means the match has been found.

I also assigned a *flippedCount* variable to keep track of how many cards have been flipped over. 

| Variables list |
| --- |
| ![variables](https://github.com/souperstition/Memory-Game-Storyline/blob/main/img/variables-list.png?raw=true) |

## :boom: Triggers [:top:](#bookmark_tabs-table-of-contents)

This is the part that took the longest. You will see that I had to repeat the same set of triggers for each note being used.

| Flipping the cards over |
| --- |
| ![flip cards](https://github.com/souperstition/Memory-Game-Storyline/blob/main/img/flip-card.png?raw=true) |

Every single card gets a trigger when it is clicked that increments the *flippedCount* variable and changes its state to "flipped".

| Disable rapid clicking |
| --- |
| ![disable rapid click](https://github.com/souperstition/Memory-Game-Storyline/blob/main/img/disable-rapid-click.png?raw=true) |

To prevent players from clicking around while the cards in play are still being flipped back over, a layer is shown for a couple of seconds which blocks players from clicking.


| Handle a match |
| --- |
| ![match found](https://github.com/souperstition/Memory-Game-Storyline/blob/main/img/handle-correct.png?raw=true) |

Once a match has been found, an animation happens off screen. That animation ending triggers both matched cards being set to the "disabled" state.

| Update the variables |
| --- |
| ![update the variables](https://github.com/souperstition/Memory-Game-Storyline/blob/main/img/setA.png?raw=true) |

Once the cards have been disabled, the note's variable will update its value to 1.

| Showing the win screen |
| --- |
| ![show win layer](https://github.com/souperstition/Memory-Game-Storyline/blob/main/img/show-win-layer.png?raw=true) |

Once all the cards in play are disabled, the win layer is shown and the user can click to play again.

| You win!  |
| --- |
| ![win screen](https://github.com/souperstition/Memory-Game-Storyline/blob/main/img/win.gif?raw=true) |

Finally, the last thing to do is reset all the variables once a new game starts.

| Resetting the variables back to 0 |
| --- |
| ![reset variables](https://github.com/souperstition/Memory-Game-Storyline/blob/main/img/new-game-reset.png?raw=true) |
 
## :keyboard: Custom JavaScript [:top:](#bookmark_tabs-table-of-contents)

I also added some custom code to each slide to add a fullscreen button, and to change the page background any time the slide background changed color.

~~~js
let fullscreen;
const slide = document.querySelector('.slide-transition-container');
const container = slide.querySelector('.slide');
let fsBtn = document.createElement('button');
fsBtn.innerHTML = 'Go Fullscreen';

// STYLES:
const css = `
    .fs-button {
        position: absolute; 
        top: 20px; 
        right: 20px; 
        z-index: 11; 
        border: 1px solid #fff; 
        border-radius: 5px; 
        font-size: 1.5rem; 
        padding: 0.5rem 0.7em; 
        background-color: rgba(0,0,0,0.2); 
        color: #fff; 
        font-family: Verdana, Geneva, Tahoma, sans-serif; 
        -webkit-font-smoothing: antialiased; 
        cursor: pointer; 
        transition: all .3s;
        width: max-content;
    }

    .fs-button:hover {
        background-color: rgba(255,255,255,0.2);
        color: #000;
    }

.wrapper {
position: relative;
z-index: 0;
}

    .slide-transition-container {
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .slide-transition-container .slide {
        position: relative;
        left: unset;
        top: unset;
    }

    .slide-layer {
        position: absolute;
        top: 0;
        left: unset;
        margin: auto;
    }

`;
const style = document.createElement('style');
if (style.styleSheet) {
    style.styleSheet.cssText = css;
} else {
    style.appendChild(document.createTextNode(css));
}
document.querySelector('head').appendChild(style);
fsBtn.classList.add('fs-button');

function toggleFullScreen() {
    if (!document.fullscreenElement) {
        // if we are in Fullscreen mode, the text should say Exit Fullscreen
        fsBtn.innerHTML = 'Exit Fullscreen';
        slide.requestFullscreen();
    } else {
      if (document.exitFullscreen) {
        // if we are not in Fullscreen mode, the text should say Go Fullscreen
fsBtn.innerHTML = 'Go Fullscreen'; 
          document.exitFullscreen();
      }
    }
  }

// this class lets us know the button has been added so that if we revisit this slide we don't get a bunch of buttons
fsBtn.classList.add('added'); 

// if we don't already have a button on the page, go ahead and add it:
if (!document.body.querySelector('.added')) { 
	slide.appendChild(fsBtn); 
    fsBtn.addEventListener('click', function (e) {
        e.preventDefault();
        toggleFullScreen();
    });
}

const player = GetPlayer();

player.SetVar("slideBg", "#B5E6F8");

const bgColor = player.GetVar("slideBg");

console.log(bgColor);

document.body.style.backgroundColor = bgColor;

~~~

## :partying_face: Results [:top:](#bookmark_tabs-table-of-contents)

As you can see, this took quite a lot of work to complete, but I was happy with how it turned out. 

When I showed the game to my flute student, the first thing she said was "It's... SO pretty!" She approved of my design, at least. But how well did the game work for helping her learn her flute fingerings? I'm happy to say that she showed incredible improvement as a result of this interaction.

She asked to play the game at the end of our lessons for weeks! It only took a couple of weeks before I noticed I no longer had to help her remember her fingerings. I'm excited to keep coming up with new ideas to help her enjoy learning!

[:top: Back to top](#)