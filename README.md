Download Link: https://assignmentchef.com/product/solved-csci-621-programming-languages-programming-assignment-2-a-recursive-descent-parser
<br>
Design and implement a Recursive Descent Parser (RDP) for the following grammar:

&lt;elist&gt; → &lt;elist&gt; , &lt;e&gt; | &lt;e&gt;

&lt;e&gt; → &lt;n&gt; ^ &lt;e&gt; | &lt;n&gt;

&lt;n&gt; → &lt;n&gt; &lt;d&gt; | &lt;d&gt;

&lt;d&gt; → 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9




Where ^ is an exponentiation operator (associate to right). This grammar generates statements of the form 2^2^3, 15, 20^2 for which the parser outputs 256 15 400.




<strong>Solution: </strong>

In order to design this RDP, the grammar should satisfy two conditions:

(1). No left recursive non-terminals (in a production of the form &lt;x&gt; → &lt;x&gt; &lt;y&gt;, &lt;x&gt; is a left recursive non-terminal). To remove left recursion:

&lt;elist&gt; → &lt;elist&gt; , &lt;e&gt; &lt;elist&gt; → &lt;e&gt; will be changed to &lt;elist&gt; → &lt;e&gt; &lt;elist_tail&gt;

&lt;elist_tail&gt; → , &lt;elist&gt;

&lt;elist_tail&gt; →

(2). No two productions having the same LHS can start with the same symbol on the RHS (This condition is not precisely stated, but it serves the purpose). To solve this problem, you need to factorize the productions:

&lt;e&gt; → &lt;n&gt; ^ &lt;e&gt; &lt;e&gt; → &lt;n&gt;

will be changed to  &lt;e&gt; → &lt;n&gt; &lt;etail&gt;

&lt;etail&gt; → ^ &lt;e&gt;

&lt;etail&gt; →

Applying the same techniques to the &lt;n&gt; productions, you get:

&lt;n&gt; → &lt;d&gt; &lt;ntail&gt;

&lt;ntail&gt; → &lt;n&gt;

&lt;ntail&gt; →

Now the grammar becomes: &lt;elist&gt; → &lt;e&gt; &lt;elist_tail&gt;

&lt;elist_tail&gt; → , &lt;elist&gt;

&lt;elist_tail&gt; →

&lt;e&gt; → &lt;n&gt; &lt;etail&gt;

&lt;etail&gt; → ^ &lt;e&gt;

&lt;etail&gt; →

&lt;n&gt; → &lt;d&gt; &lt;ntail&gt;

&lt;ntail&gt; → &lt;n&gt;

&lt;ntail&gt; →

&lt;d&gt; → 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9

(3) The Parser  an RDP is a set of mutually recursive procedures, one for parsing each non-terminal, together with some supporting procedures. In an algorithmic language, the RDP of the above grammar would be:




procedure RDPARSER; while NOT EOF  do

SUCCEEDED = TRUE;

GET_INP_LINE; (/* reads in the next input line*/)

GET_NEXT_SYMBOL; (/* returns the next input symbol */)

ELIST;  if  SUCCEEDED then SUCCESS_MESSAGE else FAILURE_MESSAGE endif

endwhile

end RDPARSER;




procedure ELIST; E;

if SUCCEEDED then ELIST_TAIL endif

end ELIST;




procedure ELIST_TAIL; if EOL  then         print E_Value else       if next_inp_symbol = ‘,’ then      print E_value;

GET_NEXT_SYMBOL;

ELIST; else SUCCEEDED = FALSE endif

endif

end ELIST_TAIL;




procedure E;

N_value = 0; N; if SUCCEEDED  then ETAIL endif end E;




procedure ETAIL; if (NOT ((next_inp_symbol = ‘ ,’) OR EOL)) then         if next_inp_symbol = ‘^’

then     GET_NEXT_SYMBOL;

E;

E_value = N_value ** E_value; else SUCCEEDED = FALSE endif

else E_value = N_value endif

end ETAIL;




procedure N; D; if SUCCEEDED

then     N_value = N_value * 10 + D_value;

NTAIL endif

end N;




procedure NTAIL; if (NOT ((next_inp_symbol = ‘^’ | ‘,’) OR EOL)) then N endif

end NTAIL;




procedure D; if next_inp_symbol is a digit then      compute D_value;

GET_NEXT-SYMBOL

else SUCCEEDED = FALSE endif

end d;




This is a simple example which explains the basic idea of RDP. There are many other issues you need to think about them. In particular, how to impose the left associativity on the binary + and – after removing the left recursion, when to evaluate the terms so that * and / associate to right, and how to detect an integer overflow or an uninitialized identifier whose value is needed to evaluate an expression. You also need to find a way with which you can specify the position of syntax error; you don’t have to specify the error type.




<strong>Note: </strong>

<ul>

 <li>Keep all variables declared globally as they are, except N_value.</li>

 <li>Declare N_value locally to procedure E.</li>

 <li>Make N_value a pass-by-value parameter to procedure ETAIL.</li>

 <li>Make N_value a pass-by-reference parameter to both procedures N and NTAIL.</li>

</ul>










The parse table for this parser is shown as follows:

<table width="664">

 <tbody>

  <tr>

   <td width="61"> </td>

   <td width="139"><strong>d</strong></td>

   <td width="83"> </td>

   <td width="93"><strong>^</strong></td>

   <td width="168"><strong>, </strong></td>

   <td width="120"><strong>$</strong></td>

  </tr>

  <tr>

   <td width="61"><strong>elist </strong></td>

   <td width="139"><strong>elist</strong>→<strong> e  elist’</strong></td>

   <td width="83"> </td>

   <td width="93"> </td>

   <td width="168"> </td>

   <td width="120"> </td>

  </tr>

  <tr>

   <td width="61"><strong>elist’</strong></td>

   <td width="139"> </td>

   <td width="83"> </td>

   <td width="93"> </td>

   <td width="168"><strong>elist’ </strong>→<strong> ,  elist</strong></td>

   <td width="120"><strong>elist’ </strong>→ </td>

  </tr>

  <tr>

   <td width="61"><strong>e</strong></td>

   <td width="139">e → n e’</td>

   <td width="83"> </td>

   <td width="93"> </td>

   <td width="168"> </td>

   <td width="120"> </td>

  </tr>

  <tr>

   <td width="61"><strong>e’</strong></td>

   <td width="139"> </td>

   <td width="83">e<sup>’</sup>→ ^ e</td>

   <td width="93"> </td>

   <td width="168">e<sup>’</sup> → </td>

   <td width="120">e<sup>’</sup> → </td>

  </tr>

  <tr>

   <td width="61"><strong>n</strong></td>

   <td width="139">n → d n’</td>

   <td width="83"> </td>

   <td width="93"> </td>

   <td width="168"> </td>

   <td width="120"> </td>

  </tr>

  <tr>

   <td width="61"><strong>n’</strong></td>

   <td width="139">n’ →n</td>

   <td width="83">n’ → </td>

   <td width="93"> </td>

   <td width="168">n’ → </td>

   <td width="120">n’ → </td>

  </tr>

 </tbody>

</table>


