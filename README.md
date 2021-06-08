# Study notes - AsyncStorage in a React Native App

## Introduction

The initial app is a boilerplate app created with:

```
 create-react-native-app my-async-storage-app
```

The goal of the app is to have a text field so the user can type in a number. When submit (app keyboard enter) is produced, the app should be able to write to a persistent storage using [AsyncStorage](https://facebook.github.io/react-native/docs/asyncstorage.html). As you see this example, be aware that React documentation warns:

```
It is recommended that you use an abstraction on top of AsyncStorage instead of AsyncStorage directly for anything more than light usage since it operates globally.
```

Read more [here](https://facebook.github.io/react-native/docs/asyncstorage.html).

## Main App

The main app just calls our List component:

```
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';
import List from './app/List.js';


export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>Hello</Text>
        <List />
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});

```

## The List component with async storage

```
import React, { Component } from 'react'
import { AsyncStorage } from "react-native";

import {
    TextInput, View, Text
} from 'react-native';

export default class List extends React.Component {

  constructor(props) {
    super(props);
    this.handleChangeText = this.handleChangeText.bind(this);
    this.handleSubmitText = this.handleSubmitText.bind(this);
    this.myRef = React.createRef();
    this.state = { dataTyped : false, dataValue: "initial state" };
  }

  componentDidMount() {
    AsyncStorage.getItem('myTinyValue', (errs, result) => {
      if (!errs) {
          if (result !== null) {
            console.log("Retrieved: " + result);
            this.setState({dataValue: result});
          }
      }
    });
  }

  componentDidUpdate() {
  }

  handleChangeText(value) {
    this.setState({dataValue:value});
  }

  handleSubmitText() {
    if(this.state.dataTyped == false) {
      this.setState({dataTyped: true});
      AsyncStorage.setItem('myTinyValue', this.state.dataValue, (err) => {
        if (err) {
          console.log(err);
        }
      });
    } else {
    }
  }

  render() {

    if(!this.state.dataTyped) {
      return (
        <View>

          <Text>Prior data: {this.state.dataValue}</Text>

          <TextInput ref = {this.myRef}
              keyboardType='numeric'
              style={{height: 60}}
              autoFocus={true}
              placeholder="Data"
              onChangeText = {(value) => {this.handleChangeText(value)} }
              onSubmitEditing={() => {this.handleSubmitText()} }
              />
        </View>
        );
    } else {
      return (
        <View>
          <Text>Data: {this.state.dataValue}</Text>
        </View>
          );
    }
  }
}

```
