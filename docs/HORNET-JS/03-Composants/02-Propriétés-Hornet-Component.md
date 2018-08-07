# Propriétés des composants Hornet

Ce document présente les différentes classes de props présentes dans Hornet Js et utilisées par les composant.
Les propriétés présentées sont celle proposées par [HTML](https://developer.mozilla.org/fr/docs/Web/HTML/Attributs) et [React](https://reactjs.org/docs/events.html).

## Interfaces de configuration

### Interfaces basiques

#### HTMLStandardConfigAttributes

Attributs de l'interface HTMLStandardConfigAttributes:

    accept?: string;
    acceptCharset?: string;
    action?: string;
    autoComplete?: string;
    charSet?: string;
    challenge?: string;
    checked?: boolean;
    defaultChecked?: boolean;
    classID?: string;
    dateTime?: string;
    default?: boolean;
    defer?: boolean;
    disabled?: boolean;
    download?: any;
    encType?: string;
    high?: number;
    href?: string;
    hrefLang?: string;
    htmlFor?: string;
    inputMode?: string;
    integrity?: string;
    is?: string;
    keyParams?: string;
    keyType?: string;
    list?: string;
    low?: number;
    manifest?: string;
    method?: string;
    multiple?: boolean;
    name?: string;
    open?: boolean;
    optimum?: number;
    pattern?: string;
    placeholder?: string;
    radioGroup?: string;
    readOnly?: boolean;
    rel?: string;
    role?: string;
    sandbox?: string;
    scrolling?: string;
    seamless?: boolean;
    sizes?: string;
    summary?: string;
    target?: string;
    type?: string;
    useMap?: string;

#### HTMLStandardFormAttributes

Attributs de l'interface HTMLStandardFormAttributes :

    form?: string;
    formAction?: string;
    formEncType?: string;
    formMethod?: string;
    formNoValidate?: boolean;
    formTarget?: string;
    noValidate?: boolean;
    required?: boolean;
    wrap?: string;

#### HTMLStandardGlobalAttributes

Attributs de l'interface HTMLStandardGlobalAttributes :

    accessKey?: string;
    className?: string;
    contentEditable?: boolean;
    contextMenu?: string;
    data?: string;
    dir?: string;
    draggable?: boolean;
    hidden?: boolean;
    id?: string;
    lang?: string;
    spellCheck?: boolean;
    style?: React.CSSProperties;
    tabIndex?: number;
    title?: string;

#### HTMLStandardMediaAttributes

Attributs de l'interface HTMLStandardMediaAttributes:

    allowFullScreen?: boolean;
    allowTransparency?: boolean;
    async?: boolean;
    autoPlay?: boolean;
    capture?: boolean;
    controls?: boolean;
    coords?: string;
    crossOrigin?: string;
    kind?: string;
    label?: string;
    loop?: boolean;
    media?: string;
    mediaGroup?: string;
    muted?: boolean;
    poster?: string;
    preload?: string;
    src?: string;
    srcLang?: string;
    srcSet?: string;
    wmode?: string;

#### HTMLStandardMetaAttributes

Attributs de l'interface HTMLStandardMetaAttributes :

    content?: string;
    httpEquiv?: string;

#### HTMLStandardPresentationAttributes

Attributs de l'interface HTMLStandardPresentationAttributes:

    alt?: string;
    autoFocus?: boolean;
    cellPadding?: number | string;
    cellSpacing?: number | string;
    cols?: number;
    colSpan?: number;
    frameBorder?: number | string;
    headers?: string;
    height?: number | string;
    icon?: string;
    marginHeight?: number;
    marginWidth?: number;
    max?: number | string;
    maxLength?: number;
    min?: number | string;
    minLength?: number;
    rows?: number;
    rowSpan?: number;
    scope?: string;
    scoped?: boolean;
    selected?: boolean;
    shape?: string;
    size?: number;
    span?: number;
    srcDoc?: string;
    start?: number;
    step?: number | string;
    width?: number | string;

#### HornetFormFieldProps
Attributs de l'interface HornetFormFieldProps

    name: string;
    label?: string;
    abbr?: string;
    groupClass?: string;
    labelClass?: string;
    fieldClass?: string;
    toolTip?: string;
    icoToolTip?: string;
    prefix?: any;
    suffix?: any;
    markRequired?: boolean;
    requiredLabel?: string;
    imgFilePath?: string;
    currentValue?: string | string[];
    errorComponent?: ComponentClass<FieldErrorProps>;
    errors?: INotificationType[];
    inline?: InlineStyle;
    reverseLabel?: boolean;

#### HTMLRDFaAttributes

Attributs de l'interface HTMLRDFaAttributes:

    about?: string;
    datatype?: string;
    inlist?: any;
    prefix?: string;
    property?: string;
    resource?: string;
    typeof?: string;
    vocab?: string;

#### HTMLNonStandardAttributes

Attributs de l'interface HTMLNonStandardAttributes:

    autoCapitalize?: string;
    autoCorrect?: string;
    autoSave?: string;
    color?: string;
    itemProp?: string;
    itemScope?: boolean;
    itemType?: string;
    itemID?: string;
    itemRef?: string;
    results?: number;
    security?: string;
    unselectable?: boolean;
    label?: string;

#### ReactBasicDOMAttributes

Attributs de l'interface ReactBasicDOMAttributes:

        children?: ReactNode;
        dangerouslySetInnerHTML?: {__html: string;};


### Compositions d'interfaces

#### HornetBasicFormFieldProps

L'interface HornetBasicFormFieldProps est composée des interfaces suivantes :

    HTMLStandardConfigAttributes,
    HTMLStandardFormAttributes,
    HTMLStandardGlobalAttributes,
    HTMLStandardPresentationAttributes,
    HornetFormFieldProps,
    ReactBasicDOMAttributes,
    ReactFormDOMAttributes,
    HornetComponentProps

Attributs supplémentaires de l'interface:

    name: string;

#### HornetWrittableProps

L'interface HornetWrittableProps est composée des interfaces suivantes :

    ReactClipboardDOMAttributes,
    ReactFormDOMAttributes,
    ReactKeyboardDOMAttributes

#### HornetClickableProps

L'interface HornetClickableProps est composée des interfaces suivantes :

    ReactBasicMouseDOMAttributes,
    ReactSelectDOMAttributes

#### HornetMediaProps

L'interface HornetMediaProps est composée des interfaces suivantes :

    HTMLStandardMediaAttributes,
    ReactMediaDOMAttributes,
    ReactImageDOMAttributes

#### HornetDraggableProps

L'interface HornetDraggableProps est composée des interfaces suivantes :

    HTMLStandardGlobalAttributes,
    ReactDragDOMAttributes,
    HornetClickableProps


#### HornetHTMLAttributes

L'interface HornetHTMLAttributes est composée des interfaces suivantes :

    HTMLStandardConfigAttributes,
    HTMLStandardFormAttributes,
    HTMLStandardGlobalAttributes,
    HTMLStandardMediaAttributes,
    HTMLStandardMetaAttributes,
    HTMLStandardPresentationAttributes,
    HTMLNonStandardAttributes,
    HTMLRDFaAttributes

#### HornetProps

L'interface HornetProps est composée des interfaces suivantes :

    HornetReactDOMAttributes,
    HornetHTMLAttributes

## Interfaces d'evennements

### Interfaces basiques

#### ReactClipboardDOMAttributes

Attributs de l'interface ReactClipboardDOMAttributes :

    onCopy?: ClipboardEventHandler< HTMLInputElement>;
    onCut?: ClipboardEventHandler< HTMLInputElement>;
    onPaste?: ClipboardEventHandler< HTMLInputElement>;


#### ReactComposeDOMAttributes

Attributs de l'interface ReactComposeDOMAttributes :

    onCompositionEnd?: CompositionEventHandler< HTMLElement>;
    onCompositionStart?: CompositionEventHandler< HTMLElement>;
    onCompositionUpdate?: CompositionEventHandler< HTMLElement>;


#### ReactFocusDOMAttributes

Attributs de l'interface ReactFocusDOMAttributes :

    onFocus?: FocusEventHandler< HTMLInputElement>;
    onBlur?: FocusEventHandler< HTMLInputElement>;


#### ReactFormDOMAttributes

Attributs de l'interface ReactFormDOMAttributes :

    onChange?: FormEventHandler< HTMLElement>;
    onInput?: FormEventHandler< HTMLElement>;
    onSubmit?: FormEventHandler< HTMLElement>;


#### ReactImageDOMAttributes

Attributs de l'interface ReactImageDOMAttributes :

    onLoad?: ReactEventHandler< HTMLElement>;
    onError?: ReactEventHandler< HTMLElement>; // also a Media Event


#### ReactKeyboardDOMAttributes

Attributs de l'interface ReactKeyboardDOMAttributes :

    onKeyDown?: KeyboardEventHandler< HTMLElement>;
    onKeyPress?: KeyboardEventHandler< HTMLElement>;
    onKeyUp?: KeyboardEventHandler< HTMLElement>;


#### ReactMediaDOMAttributes

Attributs de l'interface ReactMediaDOMAttributes :

    onError?: ReactEventHandler< HTMLElement>; // also a Media Event
    onAbort?: ReactEventHandler< HTMLMediaElement>;
    onCanPlay?: ReactEventHandler< HTMLMediaElement>;
    onCanPlayThrough?: ReactEventHandler< HTMLMediaElement>;
    onDurationChange?: ReactEventHandler< HTMLMediaElement>;
    onEmptied?: ReactEventHandler< HTMLMediaElement>;
    onEncrypted?: ReactEventHandler< HTMLMediaElement>;
    onEnded?: ReactEventHandler< HTMLMediaElement>;
    onLoadedData?: ReactEventHandler< HTMLMediaElement>;
    onLoadedMetadata?: ReactEventHandler< HTMLMediaElement>;
    onLoadStart?: ReactEventHandler< HTMLMediaElement>;
    onPause?: ReactEventHandler< HTMLMediaElement>;
    onPlay?: ReactEventHandler< HTMLMediaElement>;
    onPlaying?: ReactEventHandler< HTMLMediaElement>;
    onProgress?: ReactEventHandler< HTMLMediaElement>;
    onRateChange?: ReactEventHandler< HTMLMediaElement>;
    onSeeked?: ReactEventHandler< HTMLMediaElement>;
    onSeeking?: ReactEventHandler< HTMLMediaElement>;
    onStalled?: ReactEventHandler< HTMLMediaElement>;
    onSuspend?: ReactEventHandler< HTMLMediaElement>;
    onTimeUpdate?: ReactEventHandler< HTMLMediaElement>;
    onVolumeChange?: ReactEventHandler< HTMLMediaElement>;
    onWaiting?: ReactEventHandler< HTMLMediaElement>;


#### ReactBasicMouseDOMAttributes

Attributs de l'interface ReactBasicMouseDOMAttributes:

    onClick?: MouseEventHandler< HTMLElement>;
    onContextMenu?: MouseEventHandler< HTMLElement>;
    onDoubleClick?: MouseEventHandler< HTMLElement>;
    onMouseDown?: MouseEventHandler< HTMLElement>;
    onMouseEnter?: MouseEventHandler< HTMLElement>;
    onMouseLeave?: MouseEventHandler< HTMLElement>;
    onMouseMove?: MouseEventHandler< HTMLElement>;
    onMouseOut?: MouseEventHandler< HTMLElement>;
    onMouseOver?: MouseEventHandler< HTMLElement>;
    onMouseUp?: MouseEventHandler< HTMLElement>;

#### ReactDragDOMAttributes

Attributs de l'interface ReactDragDOMAttributes:

    onDrag?: DragEventHandler< HTMLElement >;
    onDragEnd?: DragEventHandler< HTMLElement>;
    onDragEnter?: DragEventHandler< HTMLElement>;
    onDragExit?: DragEventHandler< HTMLElement>;
    onDragLeave?: DragEventHandler< HTMLElement>;
    onDragOver?: DragEventHandler< HTMLElement>;
    onDragStart?: DragEventHandler< HTMLElement>;
    onDrop?: DragEventHandler< HTMLElement>;


#### ReactSelectDOMAttributes

Attributs de l'interface ReactSelectDOMAttributes:

    onSelect?: ReactEventHandler< HTMLElement>;


#### ReactTouchDOMAttributes

Attributs de l'interface ReactTouchDOMAttributes:

    onTouchCancel?: TouchEventHandler< HTMLElement>;
    onTouchEnd?: TouchEventHandler< HTMLElement>;
    onTouchMove?: TouchEventHandler< HTMLElement>;
    onTouchStart?: TouchEventHandler< HTMLElement>;


#### ReactScrollDOMAttributes

Attributs de l'interface ReactScrollDOMAttributes:

    onScroll?: UIEventHandler< HTMLElement>;


#### ReactWheelDOMAttributes

Attributs de l'interface ReactWheelDOMAttributes:

    onWheel?: WheelEventHandler< HTMLElement>;


#### ReactAnimationDOMAttributes

Attributs de l'interface ReactAnimationDOMAttributes:

    onAnimationStart?: AnimationEventHandler< HTMLElement>;
    onAnimationEnd?: AnimationEventHandler< HTMLElement>;
    onAnimationIteration?: AnimationEventHandler< HTMLElement>;


#### ReactTransitionDOMAttributes

Attributs de l'interface ReactTransitionDOMAttributes:

    onTransitionEnd?: TransitionEventHandler< HTMLElement>;


### Compositions d'interfaces

#### HornetReactDOMAttributes

L'interface HornetReactDOMAttributes est composée des interfaces suivantes :

    ReactClipboardDOMAttributes,
    ReactComposeDOMAttributes,
    ReactFocusDOMAttributes,
    ReactFormDOMAttributes,
    ReactImageDOMAttributes,
    ReactKeyboardDOMAttributes,
    ReactMediaDOMAttributes,
    ReactBasicMouseDOMAttributes,
    ReactDragDOMAttributes,
    ReactSelectDOMAttributes,
    ReactTouchDOMAttributes,
    ReactScrollDOMAttributes,
    ReactWheelDOMAttributes,
    ReactAnimationDOMAttributes,
    ReactTransitionDOMAttributes,
    ReactBasicDOMAttributes

#### ReactMouseDOMAttributes

L'interface ReactMouseDOMAttributes est composée des interfaces suivantes :

    ReactBasicMouseDOMAttributes,
    ReactDragDOMAttributes

