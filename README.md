```
import React from 'react';
import {
  Dimensions,
  View,
  Animated,
  ScrollView,
  Platform,
  StyleSheet,
  InteractionManager,
} from 'react-native';

import PropTypes from 'prop-types';
import DeprecatedPropTypes from 'deprecated-react-native-prop-types';

import ViewPagerOriginal from '@react-native-community/viewpager';

import SceneComponent from './SceneComponent';
import DefaultTabBar from './DefaultTabBar';
import ScrollableTabBar from './ScrollableTabBar';

const ForwardedViewPager = React.forwardRef((props, ref) => (
  <ViewPagerOriginal {...props} ref={ref} />
));

const AnimatedViewPagerAndroid = Platform.OS === 'android'
  ? Animated.createAnimatedComponent(ForwardedViewPager)
  : undefined;

class ScrollableTabView extends React.Component {
  static DefaultTabBar = DefaultTabBar;
  static ScrollableTabBar = ScrollableTabBar;

  static propTypes = {
    tabBarPosition: PropTypes.oneOf(['top', 'bottom', 'overlayTop', 'overlayBottom']),
    initialPage: PropTypes.number,
    page: PropTypes.number,
    onChangeTab: PropTypes.func,
    onScroll: PropTypes.func,
    renderTabBar: PropTypes.any,
    tabBarUnderlineStyle: DeprecatedPropTypes.ViewPropTypes.style,
    tabBarBackgroundColor: PropTypes.string,
    tabBarActiveTextColor: PropTypes.string,
    tabBarInactiveTextColor: PropTypes.string,
    tabBarTextStyle: PropTypes.object,
    style: DeprecatedPropTypes.ViewPropTypes.style,
    contentProps: PropTypes.object,
    scrollWithoutAnimation: PropTypes.bool,
    locked: PropTypes.bool,
    prerenderingSiblingsNumber: PropTypes.number,
  };

  static defaultProps = {
    tabBarPosition: 'top',
    initialPage: 0,
    page: -1,
    onChangeTab: () => {},
    onScroll: () => {},
    contentProps: {},
    scrollWithoutAnimation: false,
    locked: false,
    prerenderingSiblingsNumber: 0,
  };

  constructor(props) {
    super(props);
    this.scrollView = null;
    this.scrollOnMountCalled = false;
    this.tabWillChangeWithoutGesture = false;

    const containerWidth = Dimensions.get('window').width;
    let scrollValue, scrollXIOS, positionAndroid, offsetAndroid;

    if (Platform.OS === 'ios') {
      scrollXIOS = new Animated.Value(props.initialPage * containerWidth);
      const containerWidthAnimatedValue = new Animated.Value(containerWidth);
      containerWidthAnimatedValue.__makeNative();
      scrollValue = Animated.divide(scrollXIOS, containerWidthAnimatedValue);

      const callListeners = this._polyfillAnimatedValue(scrollValue);
      scrollXIOS.addListener(({ value }) =>
        callListeners(value / containerWidth)
      );
    } else {
      positionAndroid = new Animated.Value(props.initialPage);
      offsetAndroid = new Animated.Value(0);
      scrollValue = Animated.add(positionAndroid, offsetAndroid);

      const callListeners = this._polyfillAnimatedValue(scrollValue);
      let positionAndroidValue = props.initialPage;
      let offsetAndroidValue = 0;
      positionAndroid.addListener(({ value }) => {
        positionAndroidValue = value;
        callListeners(positionAndroidValue + offsetAndroidValue);
      });
      offsetAndroid.addListener(({ value }) => {
        offsetAndroidValue = value;
        callListeners(positionAndroidValue + offsetAndroidValue);
      });
    }

    this.state = {
      currentPage: props.initialPage,
      scrollValue,
      scrollXIOS,
      positionAndroid,
      offsetAndroid,
      containerWidth,
      sceneKeys: this.newSceneKeys({ currentPage: props.initialPage }),
    };
  }

  componentDidUpdate(prevProps) {
    if (this.props.children !== prevProps.children) {
      this.updateSceneKeys({
        page: this.state.currentPage,
        children: this.props.children,
      });
    }

    if (this.props.page >= 0 && this.props.page !== this.state.currentPage) {
      this.goToPage(this.props.page);
    }
  }

  componentWillUnmount() {
    if (Platform.OS === 'ios') {
      this.state.scrollXIOS?.removeAllListeners();
    } else {
      this.state.positionAndroid?.removeAllListeners();
      this.state.offsetAndroid?.removeAllListeners();
    }
  }

  _polyfillAnimatedValue(animatedValue) {
    const listeners = new Set();
    animatedValue.addListener = (listener) => listeners.add(listener);
    animatedValue.removeListener = (listener) => listeners.delete(listener);
    animatedValue.removeAllListeners = () => listeners.clear();
    return (value) => listeners.forEach((l) => l({ value }));
  }

  goToPage = (pageNumber) => {
    if (Platform.OS === 'ios') {
      const offset = pageNumber * this.state.containerWidth;
      this.scrollView?.scrollTo({ x: offset, y: 0, animated: !this.props.scrollWithoutAnimation });
    } else {
      if (this.scrollView) {
        this.tabWillChangeWithoutGesture = true;
        if (this.props.scrollWithoutAnimation) {
          this.scrollView.setPageWithoutAnimation(pageNumber);
        } else {
          this.scrollView.setPage(pageNumber);
        }
      }
    }

    const currentPage = this.state.currentPage;
    this.updateSceneKeys({
      page: pageNumber,
      callback: () => this._onChangeTab(currentPage, pageNumber),
    });
  };

  updateSceneKeys({ page, children = this.props.children, callback = () => {} }) {
    const newKeys = this.newSceneKeys({ previousKeys: this.state.sceneKeys, currentPage: page, children });
    this.setState({ currentPage: page, sceneKeys: newKeys }, callback);
  }

  newSceneKeys({ previousKeys = [], currentPage = 0, children = this.props.children }) {
    const newKeys = [];
    this._children(children).forEach((child, idx) => {
      const key = this._makeSceneKey(child, idx);
      if (this._keyExists(previousKeys, key) || this._shouldRenderSceneKey(idx, currentPage)) {
        newKeys.push(key);
      }
    });
    return newKeys;
  }

  _shouldRenderSceneKey(idx, currentPageKey) {
    const siblings = this.props.prerenderingSiblingsNumber;
    return idx < (currentPageKey + siblings + 1) && idx > (currentPageKey - siblings - 1);
  }

  _keyExists(sceneKeys, key) {
    return sceneKeys.includes(key);
  }

  _makeSceneKey(child, idx) {
    return `${child.props.tabLabel}_${idx}`;
  }

  _children(children = this.props.children) {
    return React.Children.toArray(children);
  }

  _composeScenes() {
    return this._children().map((child, idx) => {
      const key = this._makeSceneKey(child, idx);
      return (
        <SceneComponent
          key={child.key}
          shouldUpdated={this._shouldRenderSceneKey(idx, this.state.currentPage)}
          style={{ width: this.state.containerWidth }}
        >
          {this._keyExists(this.state.sceneKeys, key)
            ? child
            : <View tabLabel={child.props.tabLabel} />}
        </SceneComponent>
      );
    });
  }

  _onChangeTab = (prevPage, currentPage) => {
    this.props.onChangeTab({
      i: currentPage,
      ref: this._children()[currentPage],
      from: prevPage,
    });
  };

  _onScroll = (e) => {
    if (Platform.OS === 'ios') {
      const offsetX = e.nativeEvent.contentOffset.x;
      if (offsetX === 0 && !this.scrollOnMountCalled) {
        this.scrollOnMountCalled = true;
      } else {
        this.props.onScroll(offsetX / this.state.containerWidth);
      }
    } else {
      const { position, offset } = e.nativeEvent;
      this.props.onScroll(position + offset);
    }
  };

  _onMomentumScrollBeginAndEnd = (e) => {
    const offsetX = e.nativeEvent.contentOffset.x;
    const page = Math.round(offsetX / this.state.containerWidth);
    if (this.state.currentPage !== page) {
      this._updateSelectedPage(page);
    }
  };

  _updateSelectedPage = (nextPage) => {
    let page = typeof nextPage === 'object' ? nextPage.nativeEvent.position : nextPage;
    const currentPage = this.state.currentPage;
    !this.tabWillChangeWithoutGesture && this.updateSceneKeys({
      page,
      callback: () => this._onChangeTab(currentPage, page),
    });
    this.tabWillChangeWithoutGesture = false;
  };

  _handleLayout = (e) => {
    const { width } = e.nativeEvent.layout;
    if (!width || width <= 0 || Math.round(width) === Math.round(this.state.containerWidth)) {
      return;
    }

    if (Platform.OS === 'ios') {
      const containerWidthAnimatedValue = new Animated.Value(width);
      containerWidthAnimatedValue.__makeNative();
      const scrollValue = Animated.divide(this.state.scrollXIOS, containerWidthAnimatedValue);
      this.setState({ containerWidth: width, scrollValue });
    } else {
      this.setState({ containerWidth: width });
    }

    requestAnimationFrame(() => {
      this.goToPage(this.state.currentPage);
    });
  };

  renderScrollableContent() {
    const scenes = this._composeScenes();

    if (Platform.OS === 'ios') {
      return (
        <Animated.ScrollView
          horizontal
          pagingEnabled
          ref={(ref) => { this.scrollView = ref; }}
          automaticallyAdjustContentInsets={false}
          contentOffset={{ x: this.props.initialPage * this.state.containerWidth }}
          onScroll={Animated.event(
            [{ nativeEvent: { contentOffset: { x: this.state.scrollXIOS } } }],
            { useNativeDriver: true, listener: this._onScroll }
          )}
          scrollEventThrottle={16}
          scrollsToTop={false}
          showsHorizontalScrollIndicator={false}
          scrollEnabled={!this.props.locked}
          directionalLockEnabled
          alwaysBounceVertical={false}
          keyboardDismissMode="on-drag"
          {...this.props.contentProps}
        >
          {scenes}
        </Animated.ScrollView>
      );
    }

    return (
      <AnimatedViewPagerAndroid
        key={this._children().length}
        style={styles.scrollableContentAndroid}
        initialPage={this.props.initialPage}
        onPageSelected={this._updateSelectedPage}
        keyboardDismissMode="on-drag"
        scrollEnabled={!this.props.locked}
        onPageScroll={Animated.event(
          [{ nativeEvent: { position: this.state.positionAndroid, offset: this.state.offsetAndroid } }],
          { useNativeDriver: true, listener: this._onScroll }
        )}
        ref={(ref) => { this.scrollView = ref; }}
        {...this.props.contentProps}
      >
        {scenes}
      </AnimatedViewPagerAndroid>
    );
  }

  render() {
    const overlayTabs = ['overlayTop', 'overlayBottom'].includes(this.props.tabBarPosition);
    const tabBarProps = {
      goToPage: this.goToPage,
      tabs: this._children().map((child) => child.props.tabLabel),
      activeTab: this.state.currentPage,
      scrollValue: this.state.scrollValue,
      containerWidth: this.state.containerWidth,
    };

    if (this.props.tabBarBackgroundColor) tabBarProps.backgroundColor = this.props.tabBarBackgroundColor;
    if (this.props.tabBarActiveTextColor) tabBarProps.activeTextColor = this.props.tabBarActiveTextColor;
    if (this.props.tabBarInactiveTextColor) tabBarProps.inactiveTextColor = this.props.tabBarInactiveTextColor;
    if (this.props.tabBarTextStyle) tabBarProps.textStyle = this.props.tabBarTextStyle;
    if (this.props.tabBarUnderlineStyle) tabBarProps.underlineStyle = this.props.tabBarUnderlineStyle;
    if (overlayTabs) {
      tabBarProps.style = {
        position: 'absolute',
        left: 0,
        right: 0,
        [this.props.tabBarPosition === 'overlayTop' ? 'top' : 'bottom']: 0,
      };
    }

    return (
      <View style={[styles.container, this.props.style]} onLayout={this._handleLayout}>
        {this.props.tabBarPosition === 'top' && this.renderTabBar(tabBarProps)}
        {this.renderScrollableContent()}
        {(this.props.tabBarPosition === 'bottom' || overlayTabs) && this.renderTabBar(tabBarProps)}
      </View>
    );
  }

  renderTabBar(props) {
    if (this.props.renderTabBar === false) return null;
    if (this.props.renderTabBar) return React.cloneElement(this.props.renderTabBar(props), props);
    return <DefaultTabBar {...props} />;
  }
}

const styles = StyleSheet.create({
  container: { flex: 1 },
  scrollableContentAndroid: { flex: 1 },
});

export default ScrollableTabView;
```
