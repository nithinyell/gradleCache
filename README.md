```
import { View, Text, TouchableOpacity } from 'react-native';

const AccessibleFooter = () => (
  <View
    style={{ backgroundColor: 'white', padding: 20 }}
    accessible={false}
    importantForAccessibility="yes"
  >
    <TouchableOpacity
      onPress={() => console.log('Pressed')}
      accessible={true}
      accessibilityRole="button"
      accessibilityLabel="Approve Button"
      style={{ backgroundColor: 'blue', padding: 15 }}
    >
      <Text style={{ color: 'white' }}>Approve</Text>
    </TouchableOpacity>
  </View>
);
```
