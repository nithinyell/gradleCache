```
import React from 'react';
import { View, Text, TouchableOpacity, AccessibilityInfo, findNodeHandle } from 'react-native';

export default class FooterTest extends React.Component {
  buttonRef = React.createRef();

  componentDidMount() {
    setTimeout(() => {
      const handle = findNodeHandle(this.buttonRef.current);
      if (handle) AccessibilityInfo.setAccessibilityFocus(handle);
    }, 1000);
  }

  render() {
    return (
      <View
        style={{
          position: 'absolute',
          bottom: 0,
          width: '100%',
          backgroundColor: 'white',
          padding: 20,
        }}
        accessibilityViewIsModal={true}
        accessible={true}
      >
        <TouchableOpacity
          ref={this.buttonRef}
          accessible={true}
          accessibilityLabel="Sticky Approve"
          accessibilityRole="button"
          style={{ backgroundColor: 'blue', padding: 16 }}
          onPress={() => console.log('Pressed')}
        >
          <Text style={{ color: 'white' }}>Approve</Text>
        </TouchableOpacity>
      </View>
    );
  }
}

```
