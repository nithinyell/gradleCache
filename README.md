```
import React, { Component } from 'react';
import { View, ActivityIndicator } from 'react-native';
import Pdf from 'react-native-pdf';
import { Buffer } from 'buffer';

export default class PdfViewer extends Component {
  constructor(props) {
    super(props);
    this.state = {
      base64Pdf: null,
      loading: true,
    };
  }

  async componentDidMount() {
    try {
      const pdfUrl = 'https://example.com/my.pdf'; // replace with your URL
      const response = await fetch(pdfUrl);
      const arrayBuffer = await response.arrayBuffer();
      const base64String = Buffer.from(arrayBuffer).toString('base64');
      this.setState({ base64Pdf: base64String, loading: false });
    } catch (err) {
      console.error('Error fetching PDF:', err);
      // You might want to set loading:false and handle error in UI
      this.setState({ loading: false });
    }
  }

  render() {
    const { base64Pdf, loading } = this.state;

    if (loading) {
      return (
        <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
          <ActivityIndicator size="large" />
        </View>
      );
    }

    if (!base64Pdf) {
      return (
        <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
          <Pdf
            source={{ uri: `data:application/pdf;base64,` }}
            style={{ flex: 1 }}
            onError={(e) => console.log('PDF load error:', e)}
          />
        </View>
      );
    }

    const source = { uri: `data:application/pdf;base64,${base64Pdf}` };

    return (
      <Pdf
        source={source}
        style={{ flex: 1 }}
        onError={(e) => console.log('PDF load error:', e)}
      />
    );
  }
}

```
