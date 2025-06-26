# Welcome to the Advanced Inventory System

## This project is a practice program attempting to model an Inventory System of a video game using **Unreal Engine 5.**

The Inventory Systems main goal is to serve as both a _practice and training exercise_ for myself and as a guide for other that wish to implement something similar into their games/projects. This Inventory includes the following features:
* Stackable and Usable Items
* Chests and Contatiners with their own inventory.
* Transfering of items between slots and container inventroies
* Dropping items individually or in stack, the stack generates a "Loot Bag" with their own inventory.
* A hot bar in which you can drag items to Bind them to the hotbar slot
* An attempt of a save/loading system in which your inventory is saved at the end of a Game and loads the inventory at the beginning of the game
* An attempt of a save/load system where it saves the position of dropped items.

This document is meant to be a documentation of all the features as well as an explanation using Pseudo code for all the added elements of the project.

# Content

<details>

  <summary>Syntaxis, Diagrams and Media</summary>

  # Syntaxis, Diagram and Media

  ## How is this document organized and how should you read it?
  
  This section is to give an understanding of how the document will be formated and organized as well as explaining the diagrams that will be placed around this document.

  ### Syntax

  All of the added or edited functions, structures or events will be added to this document. Any default classes, structures, etc, that are in the base Unreal Engine default projects, will only be added if they are specifically mentioned in the context of the coding.

  The coding will be done in a pseudocode that will ressemble as much as possible a object oriented language

  Example:

  ```
    class newClass {

      public  bool attribute;
      public  int attribute2;
      private static  struct attribute3;

      public function1( int parameter, bool parameter){
        return returnType;
      }

      private functionEvent(){
        attribute += 1;
      }
    }
  ```
  
  ### Diagrams 
  
  There will be general UML Diagrams summarizing and showing the logic of every component, as Unreal has very specific color coding for their nodes (i.e, events, functions, and the specific color of the data structures) these diagrams will use the colors used in Unreal for the classes and data structures.
  
  Example:

  ![imagen](https://github.com/user-attachments/assets/c4eb07d7-86af-498c-9c1a-d473067e88b4) [^1]

  [^1]: Example of a diagram that will be used in this document

### Media

Media in this document will have foot notes describing what the media is showing. Media can include images, videos of the project to assist in the understanding of this project.

Example:

  ![dotboxspawnemitter](https://github.com/user-attachments/assets/a0b89f3c-2c57-42c8-b502-7a0211c958d8)[^2]

  [^2]: Example of an image showing an Unreal Project taken from the [Unreal Documentation Webpage](https://dev.epicgames.com/documentation/en-us/unreal-engine/nodes-in-unreal-engine). 
  
</details>

<details>
  
  <summary>Inventory System</summary>

  # Inventory_System

  ## The main function of the system

  This function is the core of this project, it is the system in which items are added, removed, transfered, used, etc.

  ![imagen](https://github.com/user-attachments/assets/32e7b6d2-2cfd-4077-83d2-09ac555ea428) [^3]
  
  [^3]: Inventory System Class Diagram

  ### Project Settings

  For this structure you will need to add an input into your Project settings to Interact with items, in this case we will use the "i" key to give the ability to interact with pickable items
  ![imagen](https://github.com/user-attachments/assets/b75ad283-0a00-4999-af1e-020f104a5118) [^4]

  [^4]: Image showing the project setting where in Inputs, we add the Interact Input that is set to the "i" key.

  

  <summary>Event Graph</summary>

  ### Event Graph

  This section show every single event in the Inventory System blueprint class.

  <ins>Event Begin Play</ins>

  The Begin Play event starts at the beginning of the project. It is able to load any previous saved inventories, generate the inventory size, add a display message to the viewport when an item is interactable, and auto saves the inventory. 

  ![imagen](https://github.com/user-attachments/assets/883fade5-97d6-4d10-bf7f-b4d5e6b991de)[^5]

  [^5]: Begin Play Event in Unreal Engine 5

  ```
  #import F_Slot_Struct
  #import W_Display_Message

  public class Inventory System {

    private int inventorySize = 16 
    private F_Slot_Struct[] content;
    private float interactionTrace = 300.0;
    private Actor lookAtActor;
    private W_Display_Message displayMessage;

    private eventDispatch On_Inventory_Update; // Event Dispatcher, allows multiple events to happens at once

    // starts at the beginning of the project
    public event Event_Begin_Play(){
      loadInventory(self);
      content.resize(inventorySize); //sets the contents array size to 16
      displayMessage = createWidget(classtype.W_Display_Message);
      addToViewport(displayMessage);
      bindEventTo(On_Inventory_Update);
    }
  }
  ```

  <ins>Event Tick</ins>

  This Event updates every frame and checks the interaction trace to see if there is an interactable item in front of the player.

  ![imagen](https://github.com/user-attachments/assets/82d3271a-62f8-43c7-8dd1-2f07f0346b82) [^6]

  [^6]: Tick Event in Unreal Engine 5

  
  ```
   public event Tick(){
    interactionTrace(self); //Check if an interactive item is currently in the players interactive distance
   }
  ```

  <ins>Event InputAction Interact</ins>

  This Event triggers every time the player interacts with an item with the Inpu "I".

  ![imagen](https://github.com/user-attachments/assets/06a68e27-e3be-416e-9f26-68112ac35407) [^7]

  [^7]: InputAction Interact Event in Unreal Engine 5

  ```
   public event InputAction_Interact(){ 
    switch(input Interact){ //checks if the input is pressed or released
        case "pressed":
          if(lookAtActor.isValid){
            Server_Interact(self, lookAtActor)
          }
      }
   }
  ```
  
  <summary>AddToInventory</summary>
  
</details>




