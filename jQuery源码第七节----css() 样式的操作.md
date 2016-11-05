#   (6058,6620) css() ��ʽ�Ĳ���
#### �򻯿��
```
function vendorPropName(){}
function isHidden(){}
function getStyles(){}
functions showHide(){}
jQuery.fn.extend({
    css
    show
    hide
    toggle
})
jQuery.extend({
    cssHooks
    cssNumber
    cssProps
    style
    css
});

curCSS = function(){}
function setPositiveNumber(){}
function getWidthOrHeight(){}
function css_defaultDisplay(){}
function actualDisplay(){}
һЩcssHooks
```
css     

```
jQuery.fn.extend( {
css: function( name, value ) {
            return access( this, function( elem, name, value ) {
                var styles, len,
                    map = {},
                    i = 0;
                //֧���������
                if ( jQuery.isArray( name ) ) {
                //���ĸ�����
                  <!--var getStyles = function( elem ) {-->
                  <!--    return window.getComputedStyle( elem );-->
                  <!-- };-->
                    styles = getStyles( elem );
                    len = name.length;

                    for ( ; i < len; i++ ) {
                        map[ name[ i ] ] = jQuery.css( elem, name[ i ], false, styles );
                    }

                    return map;
                }
                //�ֿ������жϣ����û��߻�ȡ
                return value !== undefined ?
                    jQuery.style( elem, name, value ) :
                    jQuery.css( elem, name );
            }, name, value, arguments.length > 1 );
        },
```
![image](http://objheplwd.bkt.clouddn.com/css.png)

## style ����

```
jQuery.extend( {
//extra �ǳߴ緽���Ĳ�����ele��Ԫ�أ�name��value
style: function( elem, name, value, extra ) {

            // �������� text and comment �ڵ�
            if ( !elem || elem.nodeType === 3 || elem.nodeType === 8 || !elem.style ) {
                return;
            }

            // Make sure that we're working with the right name
            var ret, type, hooks,
            //ת�շ�
                origName = jQuery.camelCase( name ),
                style = elem.style;
            //float
            name = jQuery.cssProps[ origName ] ||
            //�����ǰ׺
                ( jQuery.cssProps[ origName ] = vendorPropName( origName ) || origName );
            //����
            hooks = jQuery.cssHooks[ name ] || jQuery.cssHooks[ origName ];

            // ����
            if ( value !== undefined ) {
                type = typeof value;

                // Convert "+=" or "-=" to relative numbers $('#div').css('width','+=100')
                if ( type === "string" && ( ret = rcssNum.exec( value ) ) && ret[ 1 ] ) {
                    value = adjustCSS( elem, name, ret );

                    // Fixes bug #9237
                    type = "number";
                }

                // ���ò���ȷ��ֵ��ֱ�ӷ���
                if ( value == null || value !== value ) {
                    return;
                }

                //���ǵ�����ֵ  ��Щ�ǲ��ӵ�λ��
                <!--cssNumber: {-->
                <!--    "animationIterationCount": true,-->
                <!--    "columnCount": true,-->
                <!--    "fillOpacity": true,-->
                <!--    "flexGrow": true,-->
                <!--    "flexShrink": true,-->
                <!--    "fontWeight": true,-->
                <!--    "lineHeight": true,-->
                <!--    "opacity": true,-->
                <!--    "order": true,-->
                <!--    "orphans": true,-->
                <!--    "widows": true,-->
                <!--    "zIndex": true,-->
                <!--    "zoom": true-->
                <!--},-->
                if ( type === "number" ) {
                    value += ret && ret[ 3 ] || ( jQuery.cssNumber[ origName ] ? "" : "px" );
                }

                // Support: IE9-11+
                // background-* props affect original clone's values ��¡Ԫ�أ������˱�������Ӱ�챻��¡Ԫ��
                if ( !support.clearCloneStyle && value === "" && name.indexOf( "background" ) === 0 ) {
                    style[ name ] = "inherit";
                }

                // If a hook was provided, use that value, otherwise just set the specified value
                if ( !hooks || !( "set" in hooks ) ||
                    ( value = hooks.set( elem, value, extra ) ) !== undefined ) {

                    style[ name ] = value;
                }

            } else {

                // If a hook was provided get the non-computed value from there
                if ( hooks && "get" in hooks &&
                    ( ret = hooks.get( elem, false, extra ) ) !== undefined ) {

                    return ret;
                }

                // Otherwise just get the value from the style object
                return style[ name ];
            }
        },
```
## css ��ȡ


```
//elem Ԫ�� name ��css������ extra �ǳߴ�Ĳ��� styles  ���������Ҫ��styles�Ĳ���
css: function( elem, name, extra, styles ) {
            var val, num, hooks,
            //�շ�ת����ԭ��ֻ֧���շ�
                origName = jQuery.camelCase( name );

           <!--  cssProps: {-->
           <!--    "float": "cssFloat"-->
           <!--},-->
           //float�ǹؼ��֣�����ת
           //vendorPropName ������������ǰ׺
           
        <!--       cssPrefixes = [ "Webkit", "O", "Moz", "ms" ],-->
        <!--    emptyStyle = document.createElement( "div" ).style;-->
        <!--function vendorPropName( name ) {-->
        //���֧�־�ֱ�ӷ���
        <!--    if ( name in emptyStyle ) {-->
        <!--        return name;-->
        <!--    }-->
        <!--    var capName = name[ 0 ].toUpperCase() + name.slice( 1 ),-->
        <!--        i = cssPrefixes.length;-->
        <!--    while ( i-- ) {-->
        //����ǰ׺��ȥ��
        <!--        name = cssPrefixes[ i ] + capName;-->
        <!--        if ( name in emptyStyle ) {-->
        <!--            return name;-->
        <!--        }-->
        <!--    }-->
        <!--}-->

           
           
            name = jQuery.cssProps[ origName ] ||
                ( jQuery.cssProps[ origName ] = vendorPropName( origName ) || origName );

            // css���ݴ���
            hooks = jQuery.cssHooks[ name ] || jQuery.cssHooks[ origName ];
            if ( hooks && "get" in hooks ) {
                val = hooks.get( elem, true, extra );
            }

            // ����ֱ�Ӷ�����undefined����û�в�������ֱ�ӵ���curCSS
            if ( val === undefined ) {
                val = curCSS( elem, name, styles );
            }

            // ���� "normal" Ҫת���ɶ�Ӧ����ֵ
            <!--    cssNormalTransform = {-->
            <!--    letterSpacing: "0",-->
            <!--    fontWeight: "400"-->
            <!--},-->
            if ( val === "normal" && name in cssNormalTransform ) {
                val = cssNormalTransform[ name ];
            }

            // ȥ��px�ȳߴ�
            if ( extra === "" || extra ) {
                num = parseFloat( val );
                return extra === true || isFinite( num ) ? num || 0 : val;
            }
            return val;
        }
```

## curCSS

```
function curCSS( elem, name, computed ) {
        var width, minWidth, maxWidth, ret,
            style = elem.style;
    //�����������Ƕ������
        computed = computed || getStyles( elem )��
        //��ȡ
        ret = computed ? computed.getPropertyValue( name ) || computed[ name ] : undefined;

        // Support: Opera 12.1x only
        // Fall back to style even without computed
        // computed is undefined for elems on document fragments
        if ( ( ret === "" || ret === undefined ) && !jQuery.contains( elem.ownerDocument, elem ) ) {
            ret = jQuery.style( elem, name );
        }

        // Support: IE9
        // getPropertyValue is only needed for .css('filter') ����ie9��͸����
        if ( computed ) {

            // A tribute to the "awesome hack by Dean Edwards"
            // Android Browser returns percentage for some values,
            // but width seems to be reliably pixels.
            // This is against the CSSOM draft spec:
            // http://dev.w3.org/csswg/cssom/#resolved-values
            if ( !support.pixelMarginRight() && rnumnonpx.test( ret ) && rmargin.test( name ) ) {

                // Remember the original values
                width = style.width;
                minWidth = style.minWidth;
                maxWidth = style.maxWidth;

                // Put in the new values to get a computed value out
                style.minWidth = style.maxWidth = style.width = ret;
                ret = computed.width;

                // Revert the changed values
                style.width = width;
                style.minWidth = minWidth;
                style.maxWidth = maxWidth;
            }
        }

        return ret !== undefined ?

            // Support: IE9-11+
            // IE returns zIndex value as an integer.
        ret + "" :
            ret;
    }
```
## show hide toggle 


```
        show: function() {
            return showHide( this, true );
        },
        hide: function() {
            return showHide( this );
        },
        toggle: function( state ) {
            if ( typeof state === "boolean" ) {
                return state ? this.show() : this.hide();
            }
            //isHiddenͨ�������ж��Ƿ�������
            <!--var isHidden = function( elem, el ) {-->
            <!--  elem = el || elem;-->
            <!--  return jQuery.css( elem, "display" ) === "none" ||-->
            <!--!jQuery.contains( elem.ownerDocument, elem );-->
            <!--};-->
            return this.each( function() {
                if ( isHidden( this ) ) {
                    jQuery( this ).show();
                } else {
                    jQuery( this ).hide();
                }
            } );
        }
```
## showHide

```
//�еڶ�����������show��û�о���hide
function showHide( elements, show ) {
        var display, elem, hidden,
            values = [],
            index = 0,
            length = elements.length;

        for ( ; index < length; index++ ) {
            elem = elements[ index ];
            if ( !elem.style ) {
                continue;
            }

            values[ index ] = dataPriv.get( elem, "olddisplay" );
            display = elem.style.display;
            if ( show ) {

                // Reset the inline display of this element to learn if it is
                // being hidden by cascaded rules or not
                if ( !values[ index ] && display === "none" ) {
                    elem.style.display = "";
                }

                // Set elements which have been overridden with display: none
                // in a stylesheet to whatever the default browser style is
                // for such an element
                if ( elem.style.display === "" && isHidden( elem ) ) {
                    values[ index ] = dataPriv.access(
                        elem,
                        "olddisplay",
                        //�õ�Ĭ�ϵ�display
                        defaultDisplay( elem.nodeName )
                    );
                }
            } else {
                hidden = isHidden( elem );

                if ( display !== "none" || !hidden ) {
                    dataPriv.set(
                        elem,
                        "olddisplay",
                        hidden ? display : jQuery.css( elem, "display" )
                    );
                }
            }
        }

        // Set the display of most of the elements in a second loop
        // to avoid the constant reflow
        for ( index = 0; index < length; index++ ) {
            elem = elements[ index ];
            if ( !elem.style ) {
                continue;
            }
            if ( !show || elem.style.display === "none" || elem.style.display === "" ) {
                elem.style.display = show ? values[ index ] || "" : "none";
            }
        }

        return elements;
    }

```
## cssHooks

```
//���͸����  �����null����ֱ�����1
cssHooks: {
            opacity: {
                get: function( elem, computed ) {
                    if ( computed ) {

                        // We should always get a number back from opacity
                        var ret = curCSS( elem, "opacity" );
                        return ret === "" ? "1" : ret;
                    }
                }
            }
        },
```

## width
- width()   width
- innerWidth()  width + padding   ����ʱ  width = num - padding
- outerWidht()  width + padding + border    ����ʱ  width = num - padding - border
- outerWidth(true) width + padding +��border + margin  ����ʱ  width = num - padding - border - margin
![image](http://objheplwd.bkt.clouddn.com/width.png)

```
extra������content pading border margin�ĸ�����

function setPositiveNumber( elem, value, subtract ) {
        var matches = rnumsplit.exec( value );
        return matches ?
            // Guard against undefined "subtract", e.g., when used as in cssHooks
        Math.max( 0, matches[ 1 ] - ( subtract || 0 ) ) + ( matches[ 2 ] || "px" ) :
            value;
    }

    function augmentWidthOrHeight( elem, name, extra, isBorderBox, styles ) {
        var i = extra === ( isBorderBox ? "border" : "content" ) ?
                // If we already have the right measurement, avoid augmentation
                4 :
                // Otherwise initialize for horizontal or vertical properties
                name === "width" ? 1 : 0,

            val = 0;

        for ( ; i < 4; i += 2 ) {
            // both box models exclude margin, so add it if we want it
            if ( extra === "margin" ) {
                val += jQuery.css( elem, extra + cssExpand[ i ], true, styles );
            }

            if ( isBorderBox ) {
                // border-box includes padding, so remove it if we want content
                if ( extra === "content" ) {
                    val -= jQuery.css( elem, "padding" + cssExpand[ i ], true, styles );
                }

                // at this point, extra isn't border nor margin, so remove border
                if ( extra !== "margin" ) {
                    val -= jQuery.css( elem, "border" + cssExpand[ i ] + "Width", true, styles );
                }
            } else {
                // at this point, extra isn't content, so add padding
                val += jQuery.css( elem, "padding" + cssExpand[ i ], true, styles );

                // at this point, extra isn't content nor padding, so add border
                if ( extra !== "padding" ) {
                    val += jQuery.css( elem, "border" + cssExpand[ i ] + "Width", true, styles );
                }
            }
        }

        return val;
    }

    function getWidthOrHeight( elem, name, extra ) {

        // Start with offset property, which is equivalent to the border-box value
        var valueIsBorderBox = true,
            val = name === "width" ? elem.offsetWidth : elem.offsetHeight,
            styles = getStyles( elem ),
            isBorderBox = jQuery.support.boxSizing && jQuery.css( elem, "boxSizing", false, styles ) === "border-box";

        // some non-html elements return undefined for offsetWidth, so check for null/undefined
        // svg - https://bugzilla.mozilla.org/show_bug.cgi?id=649285
        // MathML - https://bugzilla.mozilla.org/show_bug.cgi?id=491668
        if ( val <= 0 || val == null ) {
            // Fall back to computed then uncomputed css if necessary
            val = curCSS( elem, name, styles );
            if ( val < 0 || val == null ) {
                val = elem.style[ name ];
            }

            // Computed unit is not pixels. Stop here and return.
            if ( rnumnonpx.test(val) ) {
                return val;
            }

            // we need the check for style in case a browser which returns unreliable values
            // for getComputedStyle silently falls back to the reliable elem.style
            valueIsBorderBox = isBorderBox && ( jQuery.support.boxSizingReliable || val === elem.style[ name ] );

            // Normalize "", auto, and prepare for extra
            val = parseFloat( val ) || 0;
        }

        // use the active box-sizing model to add/subtract irrelevant styles
        return ( val +
                augmentWidthOrHeight(
                    elem,
                    name,
                    extra || ( isBorderBox ? "border" : "content" ),
                    valueIsBorderBox,
                    styles
                )
            ) + "px";
    }



```
