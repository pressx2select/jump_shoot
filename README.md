# **Jump_Shoot**

## **Overview**

This is a simple 2D fighting game built using Gemini 2.5 Flash with HTML, CSS, and JavaScript, featuring multiple unique characters, each with their own special abilities and playstyles. Battle against the AI or challenge a friend in two-player mode

## **Features**

* **Multiple Characters:** Choose from a roster of distinct characters:  
  * **Standard Character:** A balanced fighter with a basic fireball.  
  * **Bouncing Character:** Shoots fireballs that bounce off the ground and ceiling.  
  * **Curving Character:** Launches fireballs that curve upwards or downwards based on the player's jump state.  
  * **Reflect Character:** Can activate a shield to reflect incoming fireballs, gaining super meter on successful reflections. Their super is a large, slow fireball.  
* **Super Meters:** Each character builds a super meter that, when full, allows them to unleash a powerful super attack.  
* **Strength-Based Fireball Collisions:** Fireballs now have a "strength" attribute. When two fireballs collide, the stronger one prevails (reducing its strength) or both are destroyed if their strengths are equal. Reflected fireballs gain strength, making them more potent.  
* **Round-Based Gameplay:** Compete in rounds, with the first player to win a set number of rounds declared the victor.  
* **Dynamic Timer:** The game timer influences fireball cooldowns and super meter growth, adding strategic depth as the round progresses.  
* **Responsive Design:** The game adapts to different screen sizes, ensuring a playable experience on various devices.  
* **AI Opponents:** In 1-player mode, face a progression of AI opponents with different character types.

## **How to Play**

### **Controls**

* **Player 1:**  
  * **Jump:** A key or "JUMP" button  
  * **Fireball/Special:** S key or "FIREBALL" button  
* **Player 2 (2-Player Mode):**  
  * **Jump:** K key or "JUMP" button  
  * **Fireball/Special:** L key or "FIREBALL" button

### **Game Modes**

* **1 PLAYER:** Challenge the AI. You'll face a series of opponents, each with a different character type.  
* **2 PLAYERS:** Battle a friend on the same device.

### **Objective**

Reduce your opponent's health to zero to win a round. The first player to win 2 rounds wins the game set\! If the time runs out, the player with more health wins the round.

## **Character Abilities**

* **Standard Character:** Simple, effective fireball.  
* **Bouncing Character:** Fireballs bounce off the ground and ceiling.  
* **Curving Character:** Fireballs curve based on whether you shoot from the ground (upwards curve) or in the air (downwards curve).  
* **Reflect Character:**  
  * **Normal Fireball Button:** Activates a temporary shield that reflects incoming fireballs. Costs a small amount of super meter to maintain.  
  * **Super:** Fires a large, slow, high-strength fireball.

## **Development**

This game is developed using pure HTML, CSS, and JavaScript.

Used Gemini 2.5 Flash to build and completed in 2 days

## **License**

This project is open-source and available under the MIT License.

Enjoy the game\!
