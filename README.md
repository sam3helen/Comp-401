# Comp-401

package a4jedi;

import org.junit.Test;

import java.io.ByteArrayOutputStream;
import java.io.PrintStream;

import static org.junit.Assert.*;

/**
 * Created by Chris Burgess on 10/3/16.
 * Email: chrisbur@live.unc.edu
 */
public class A4Test {

    private final ByteArrayOutputStream outContent = new ByteArrayOutputStream();

    @org.junit.Before
    public void setUp() throws Exception {
        System.setOut(new PrintStream(outContent));
    }

    @org.junit.After
    public void tearDown() throws Exception {
        System.setOut(null);
    }


    @Test
    public void subPictureImplNull() {
        try {
            SubPicture picture = new SubPictureImpl(null, 10, 10, 20, 20);
            fail("Subpicture passed null and error not thrown");
        } catch (Exception e) {
        }
    }

    @Test
    public void subPictureImplBadParemeters() {
        Picture testPicture = new PictureImpl(6, 6);

        //xoffset below 0
        try {
            SubPicture picture = testPicture.extract(-2, 1, 1, 1);
            fail("Subpicture passed xOffset of -2 and did not fail");
        } catch (Exception e) {
        }
        //yoffset below 0
        try {
            SubPicture picture = testPicture.extract(1, -2, 1, 1);
            fail("Subpicture passed yOffset of -2 and did not fail");
        } catch (Exception e) {
        }

        //xoffset above height

        try {
            SubPicture picture = testPicture.extract(7, 1, 1, 1);
            fail("Subpicture passed xOffset of 7 (0-5) and did not fail");
        } catch (Exception e) {
        }
        //yoffset above height

        try {
            SubPicture picture = testPicture.extract(1, 10, 1, 1);
            fail("Subpicture passed yOffset of 10 (0-5) and did not fail");
        } catch (Exception e) {
        }

        //width and xoffset out of bounds
        try {
            SubPicture picture = testPicture.extract(1, 1, 6, 1);
            fail("Subpicture passed width of 6 + 1 (0-5) and did not fail");
        } catch (Exception e) {
        }
        //height and yoffset out of bounds
        try {
            SubPicture picture = testPicture.extract(1, 1, 1, 6);
            fail("Subpicture passed width of 6 + 1 (0-5) and did not fail");
        } catch (Exception e) {
        }

        //test super width and super height
        try {
            SubPicture picture = new SubPictureImpl(new PictureImpl(-10, -20), 10, 10, 20, 20);
            fail("Subpicture passed null and error not thrown");
        } catch (Exception e) {
        }

    }

    @Test
    public void subPictureTest() {
        Picture picture = new PictureImpl(20, 5);
        picture.setPixel(2, 1, new GrayPixel(.2));
        picture.setPixel(10, 3, new GrayPixel(.1));
        SubPicture subPicture = picture.extract(2, 1, 9, 3);
        for (int x = 0; x < subPicture.getWidth(); x++) {
            subPicture.setPixel(x, 1, new GrayPixel(.3));
        }
        subPicture.print();
        picture.print();
        String output = "X>>>>>>>>\n" +
                "DDDDDDDDD\n" +
                ">>>>>>>>M\n" +
                ">>>>>>>>>>>>>>>>>>>>\n" +
                ">>X>>>>>>>>>>>>>>>>>\n" +
                ">>DDDDDDDDD>>>>>>>>>\n" +
                ">>>>>>>>>>M>>>>>>>>>\n" +
                ">>>>>>>>>>>>>>>>>>>>\n";
        assertEquals(output, outContent.toString());

    }

    @Test
    public void subPictureCountTest() {
        Picture picture = new PictureImpl(20, 5);
        picture.setPixel(2, 1, new GrayPixel(.2));
        picture.setPixel(0, 0, new GrayPixel(.2));
        picture.setPixel(10, 3, new GrayPixel(.1));
        SubPicture subPicture = picture.extract(2, 1, 9, 3);
        for (int x = 0; x < subPicture.getWidth(); x++) {
            subPicture.setPixel(x, 1, new GrayPixel(.3));
        }
        assertEquals(11, subPicture.countRange(.1, .4));
    }

    @Test
    public void horizontalPictureHeightCheck() {
        Picture picture = new PictureImpl(10, 5);
        Picture picture1 = new PictureImpl(20, 6);
        try {
            Picture hPic = new HorizontalStackPicture(picture, picture1);
            fail("Horizontal pictures should fail if they dont have the same height.");
        } catch (Exception e) {
        }
    }

    @Test
    public void verticalPictureWidthCheck() {
        Picture picture = new PictureImpl(8, 20);
        Picture picture1 = new PictureImpl(9, 20);
        try {
            Picture hPic = new VerticalStackPicture(picture, picture1);
            fail("Vertical pictures should fail if they dont have the same width.");
        } catch (Exception e) {
        }
    }

    @Test
    public void horizontalPictureTest() {
        Picture picture = new PictureImpl(10, 5);
        picture.setPixel(2, 1, new GrayPixel(.2));
        picture.setPixel(9, 3, new GrayPixel(.1));
        Picture picture1 = new PictureImpl(3, 5);
        picture1.setPixel(0, 0, new GrayPixel(.6));
        Picture horPic = new HorizontalStackPicture(picture, picture1);
        for (int x = 0; x < horPic.getWidth(); x++) {
            horPic.setPixel(x, 2, new GrayPixel(.7));
        }
        horPic.print();
        picture.print();
        picture1.print();

        String output = ">>>>>>>>>>s>>\n" +
                ">>X>>>>>>>>>>\n" +
                ":::::::::::::\n" +
                ">>>>>>>>>M>>>\n" +
                ">>>>>>>>>>>>>\n" +
                ">>>>>>>>>>\n" +
                ">>X>>>>>>>\n" +
                "::::::::::\n" +
                ">>>>>>>>>M\n" +
                ">>>>>>>>>>\n" +
                "s>>\n" +
                ">>>\n" +
                ":::\n" +
                ">>>\n" +
                ">>>\n";
        assertEquals(output, outContent.toString());
    }

    @Test
    public void verticlePictureTest() {
        Picture picture = new PictureImpl(10, 5);
        picture.setPixel(2, 1, new GrayPixel(.2));
        picture.setPixel(9, 3, new GrayPixel(.1));
        picture.setPixel(0, 0, new GrayPixel(.6));
        Picture picture1 = new PictureImpl(10, 2);
        picture1.setPixel(0, 0, new GrayPixel(.6));
        picture1.setPixel(1, 1, new GrayPixel(.8));
        Picture horPic = new VerticalStackPicture(picture, picture1);
        for (int y = 0; y < horPic.getHeight(); y++) {
            horPic.setPixel(4, y, new GrayPixel(0));
        }
        horPic.print();
        picture.print();
        picture1.print();
        String output = "s>>>#>>>>>\n" +
                ">>X>#>>>>>\n" +
                ">>>>#>>>>>\n" +
                ">>>>#>>>>M\n" +
                ">>>>#>>>>>\n" +
                "s>>>#>>>>>\n" +
                ">->>#>>>>>\n" +
                "s>>>#>>>>>\n" +
                ">>X>#>>>>>\n" +
                ">>>>#>>>>>\n" +
                ">>>>#>>>>M\n" +
                ">>>>#>>>>>\n" +
                "s>>>#>>>>>\n" +
                ">->>#>>>>>\n";
        assertEquals(output, outContent.toString());
    }

    @Test
    public void horizontalSubTest() {
        Picture picture = new PictureImpl(10, 5);
        picture.setPixel(2, 1, new GrayPixel(.2));
        picture.setPixel(9, 3, new GrayPixel(.1));
        Picture picture1 = new PictureImpl(3, 5);
        picture1.setPixel(0, 0, new GrayPixel(.6));
        Picture horPic = new HorizontalStackPicture(picture, picture1);
        for (int x = 0; x < horPic.getWidth(); x++) {
            horPic.setPixel(x, 2, new GrayPixel(.7));
        }
        Picture subPicture = horPic.extract(3, 1, 6, 3);
        for (int x = 0; x < subPicture.getWidth(); x++) {
            for (int y = 0; y < subPicture.getHeight(); y++) {
                subPicture.setPixel(x, y, new GrayPixel(0));
            }

        }
        horPic.print();
        String output = ">>>>>>>>>>s>>\n" +
                ">>X######>>>>\n" +
                ":::######::::\n" +
                ">>>######M>>>\n" +
                ">>>>>>>>>>>>>\n";
        assertEquals(output, outContent.toString());
    }

    @Test
    public void horizontalSubCountTest() {
        Picture picture = new PictureImpl(10, 5);
        picture.setPixel(2, 1, new GrayPixel(.2));
        picture.setPixel(9, 3, new GrayPixel(.1));
        Picture picture1 = new PictureImpl(3, 5);
        picture1.setPixel(0, 0, new GrayPixel(.6));
        Picture horPic = new HorizontalStackPicture(picture, picture1);
        for (int x = 0; x < horPic.getWidth(); x++) {
            horPic.setPixel(x, 2, new GrayPixel(.7));
        }
        Picture subPicture = horPic.extract(3, 1, 6, 3);
        for (int x = 0; x < subPicture.getWidth(); x++) {
            for (int y = 0; y < subPicture.getHeight(); y++) {
                subPicture.setPixel(x, y, new GrayPixel(0));
            }

        }
        int answer = 9;
        int x = horPic.countRange(.1, .4);
        int y = subPicture.countRange(.1, .9);
        int z = picture.countRange(.6, .8);
        int a = picture1.countRange(.65, .7);
        int count = a + x + y + z;
        assertEquals(answer, count);

    }

    @Test
    public void thresholdTest() {
        Picture picture = new PictureImpl(10, 5);
        picture.setPixel(0, 0, new GrayPixel(.3));
        picture.setPixel(1, 0, new GrayPixel(.4));
        picture.setPixel(0, 1, new GrayPixel(.6));
        picture.setPixel(1, 1, new GrayPixel(.7));
        picture.setPixel(9, 4, new GrayPixel(.8));
        for (int x = 0; x < picture.getWidth(); x++) {
            picture.setPixel(x, 2, new GrayPixel(.7));
        }
        Picture transPicture = new TransformedPicture(picture, new Threshold(.66));
        transPicture.print();
        String output = "##########\n" +
                "# ########\n" +
                "          \n" +
                "##########\n" +
                "######### \n";
        assertEquals(output, outContent.toString());
    }

    @Test
    public void gammaTest() {
        Picture picture = new PictureImpl(10, 5);
        picture.setPixel(0, 0, new GrayPixel(.3));
        picture.setPixel(1, 0, new GrayPixel(.4));
        picture.setPixel(0, 1, new GrayPixel(.6));
        picture.setPixel(1, 1, new GrayPixel(.7));
        picture.setPixel(9, 4, new GrayPixel(.8));
        for (int x = 0; x < picture.getWidth(); x++) {
            picture.setPixel(x, 2, new GrayPixel(.7));
        }
        Picture transPicture = new TransformedPicture(picture, new GammaCorrect(.5));
        transPicture.print();
        String output = "#MXXXXXXXX\n" +
                "D<XXXXXXXX\n" +
                "<<<<<<<<<<\n" +
                "XXXXXXXXXX\n" +
                "XXXXXXXXXs\n";
        assertEquals(output, outContent.toString());
    }

    @Test
    public void transformExceptionTest() {
        try {
            Picture transPicture = new TransformedPicture(new PictureImpl(3, 4), new GammaCorrect(.5));
            transPicture.setPixel(1, 1, new GrayPixel(.3));
            fail("Transform should be unable to set a pixel");
        } catch (Exception e) {

        }
    }



    //getters and setters testing

    @Test
    public void subPictureGetterSetterTest(){
        Picture picture = new PictureImpl(5,5);
        picture.setPixel(1,2,new GrayPixel(.3));
        Picture sub = picture.extract(1,2,2,2);
        sub.setPixel(1,1, new GrayPixel(.8));
        double output = 1.1;
        double a = sub.getPixel(0, 0).getIntensity();
        double b = picture.getPixel(2, 3).getIntensity();
        assertEquals(output, a+b, .001);
    }

    @Test
    public void horizontalPictureGetterSetterTest(){
        Picture picture = new PictureImpl(5,5);
        Picture picture1 = new PictureImpl(10,5);
        picture.setPixel(4,1, new GrayPixel(.1));
        picture1.setPixel(0,1, new GrayPixel(.9));
        Picture horPic = new HorizontalStackPicture(picture, picture1);
        double a = horPic.getPixel(4, 1).getIntensity();
        double b = horPic.getPixel(5, 1).getIntensity();
        double answer = 1.0;
        assertEquals(answer, a + b, .001);
    }


}
