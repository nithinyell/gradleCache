Import Buffer and fetch the PDF as an ArrayBuffer

```
import { Buffer } from 'buffer';

const url = 'https://example.com/my.pdf';

async function fetchPdfAsBase64(pdfUrl) {
  try {
    const response = await fetch(pdfUrl);
    // In React Native >=0.64, fetch().arrayBuffer() is supported:
    const arrayBuffer = await response.arrayBuffer();
    // Convert ArrayBuffer to Base64
    const base64String = Buffer.from(arrayBuffer).toString('base64');
    return base64String;
  } catch (err) {
    console.error('Error fetching PDF:', err);
    throw err;
  }
}
```

Pass the Base64 string into react-native-pdf

```
import React, { useEffect, useState } from 'react';
import { View, ActivityIndicator } from 'react-native';
import Pdf from 'react-native-pdf';

export default function PdfViewer() {
  const [base64Pdf, setBase64Pdf] = useState(null);

  useEffect(() => {
    fetchPdfAsBase64('https://example.com/my.pdf')
      .then((b64) => setBase64Pdf(b64))
      .catch((e) => console.warn(e));
  }, []);

  if (!base64Pdf) {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <ActivityIndicator size="large" />
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
```
