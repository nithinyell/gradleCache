```
<ListItem
      icon
      last
      onPress={onPress}
      style={styles.itemStyle}
      accessible
      focusable
      // 👉 role matches your screenshot
      accessibilityRole={depositCheque ? 'link' : 'button'}
      accessibilityLabel={a11yLabel}
      accessibilityHint={
        depositCheque
          ? (Platform.OS === 'android' ? 'opens in web browser' : undefined)
          : undefined
      }
    >
      <Left style={styles.itemIconStyle}>
        {zelleTransfer
          ? <Image source={source} style={{ height: 25, width: 25 }} />
          : <Image source={source} />
        }
      </Left>

      {/* Make parent the only accessible node */}
      <Body
        style={styles.itemBodyStyle}
        accessibilityElementsHidden
        importantForAccessibility="no-hide-descendants"
      >
        <View style={styles.titleAndCountContainer}>
          <Text style={styles.textStyle} numberOfLines={2}>{text}</Text>
          {typeof count === 'string' && count.length > 0 ? (
            <View style={styles.approvalCountContainer}>
              <Text>{count}</Text>
            </View>
          ) : null}
        </View>

        {zelleTransfer ? (
          <View style={{ flexDirection: 'row', marginTop: 8 }}>
            <Text style={styles.textStyleItalic}>{'›'}</Text>
          </View>
        ) : null}

        {/* keep your existing trailing text */}
        <Text style={styles.textStyle}>
          {depositCheque ? (Platform.OS === 'ios' ? 'link' : 'button') : null}
        </Text>
      </Body>
    </ListItem>
```
