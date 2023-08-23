﻿# Covisian Test

[Try the project here](https://covisiantest.altervista.org/ ) 
[**Important notes for usage**: you can use the credentials admin/password to login; the first WebGL startup may take some minutes.]

The updates in this branch are marked by ***UPDATE***  for convenience.

This project allows a user to authenticate using a simple form and access a WebGL Unity build.

The latter is a simple interactive experience featuring a procedurally animated 3D creature, that follows a particle system located in the last-clicked position on the screen.

The 3D model is also uploaded in OpenSea as an NFT product.
[Go to OpenSea](https://opensea.io/assets/ethereum/0x495f947276749ce646f68ac8c248420045cb7b5e/107622308839406592901152126171654789632793357150076407710282738827484788162561) 

# Code

This sample work consists in three web-pages: 

## index.html

Contains a simple form that allow users to authenticate with an username and a password.

## login.php

A php script will check if the inserted username and password are present in the user database.

For the sake of this test, you can use the credentials “admin” and “password”, which are stored as a tuple inside the database.

In case the check returns true, the php script embeds a Unity build using an iframe, and passes the username as a parameter

## build.html

This page, embedded inside the iframe, is generated by Unity when building a WebGL application. The javascript code inside it allows the client to download the needed resources and start the application.

Moreover, this page is further edited to catch the username parameter that login.php passes. This string is used to run a method of a GameObject inside the application that shows a custom welcome message before starting the interactive experience. 


# Unity WebGL application 
The first scene contains a custom welcome message and a button to start the interactive experience.

***UPDATE*** 
**When the button is pressed, a loading message appears and the next (addressable) scene is loaded asynchronously using the Unity addressable assets management package.**

The 3D creature in this scene was modelled, textured and rigged using Blender, and then exported in .fbx format. Here is a list of the most important GameObjects :

## Target

This GameObject is a simple particle system controlled by the user’s mouse clicks. The click screen position is converted in the world space using the function call `ScreenToWorldPoint`; the depth is a random value inside a reasonable range to vary the creature movements and rotations and make the more interesting.

## Controller

This GameObject is responsible of the behavior of the creature. Specifically, it leverages Unity’s physics system to float towards a target with inertia and delay: this technique will make he movements more organic and believable.

The rotation of the object smoothly faces the target over time, and a force is applied along its forward direction:

  `transform.rotation = Quaternion.Slerp(transform.rotation, _lookrotation, turn.Evaluate(turnTimer/turnDuration)); _rigidbody.AddForceAtPosition(Vector3.ClampMagnitude(transform.forward, maxForce) * forceIntensity, position, ForceMode.Force);`

 The `drag` property from Unity's RigidBody is then used to steer or brake when the user changes the target position or the target is too close (otherwise the movement could slowly diverge).
`_rigidbody.drag += turnBrakingRate*Time.deltaTime; // when the target changes position`
`_rigidbody.drag = moveBrakingRate / Vector3.Distance(targetPosition, position);`

## Oscillator

This object is constrained to follow the controller position and rotation, but the former is vertically offset using a sine function to make the creature’s movements wavy.
## NFT

This GameObject is the parent of the NFT 3D creature. It is constrained to follow the oscillator position and rotation. The bones of the rig have the spring bones scripts attached: this code, distributed by Unity developers and customised as needed, allow the rig to be procedurally animated. In particular, it makes each bone copy the movement of the previous chained bone and delay it to automatically achieve the *overlapping* animation.

***UPDATE***
**This scene now connects to OpenSea using an API to retrieve information about the nft. In this test, the thumbnail of the nft is downloaded and shown in the scene camera.**

# Known issues
- Mobile browser are, in general, not supported as far as the interactive application is concerned.
- Accessing the build URL allows anyone to start the experience without having to authenticate: this issue is voluntarily neglected as it doesn’t fit the scope of this demonstration.

