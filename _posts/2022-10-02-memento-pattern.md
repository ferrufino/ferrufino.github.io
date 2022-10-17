---
layout: post
title: Memento Pattern
tags: iosDesignPatterns, behavioralPatterns, mementoPattern
math: false
toc: true
date: 2022-10-03 20:22 +0800
---

Memento Pattern captures the current state of an object and store it in such a way that you can retrieve it at a later time when you would like to return to the previous state.

1. Originator: Allows object to be saved and restored. Object to be saved.
2. Memento represents a stored state. Save state
3. Care taker: requests a save from originator and receives a memento in response.
   - Handles, persists, and retrieves mementos
   - Responsible for persistent the memento

providing memento back to the originator. To restore originator state.
