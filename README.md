# Firebase-Authentication

import React, { useState, useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import { auth, googleProvider, signInWithEmailAndPassword } from '../firebaseConfig.js';
import logo from '../images/ShariaStocks-logo/logo.png';
import '../auth.css';
import {
  Container, Paper, TextField, Button, Typography, Box, Link, Alert, CircularProgress
} from '@mui/material';

const Login = () => {
  const [formData, setFormData] = useState({ email: '', password: '' });
  const { email, password } = formData;
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);
  const navigate = useNavigate();

  useEffect(() => {
    if (localStorage.getItem('userEmail')) {
      navigate('/Dashboard');
    }
  }, [navigate]);

  const onChange = (e) => setFormData({ ...formData, [e.target.name]: e.target.value });

  const onSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);
    try {
      const userCredential = await signInWithEmailAndPassword(auth,email, password);
      console.log('Login successful!', userCredential.user);
      localStorage.setItem('userEmail', userCredential.user.email);
      navigate('/Dashboard');
      setFormData({ email: '', password: '' });
    } catch (err) {
      console.error('Login error:', err.message);
      setError(err.message || 'Error logging in');
    } finally {
      setLoading(false);
    }
  };

  const handleGoogleSignIn = async () => {
    try {
      const result = await auth.signInWithPopup(googleProvider);
      const user = result.user;
      console.log('Google Sign-In successful!', user);
      localStorage.setItem('userEmail', user.email);
      navigate('/Dashboard');
    } catch (err) {
      console.error('Google Sign-In error:', err.message);
      setError(err.message || 'Failed to sign in with Google');
    }
  };

  return (
    <div>
      <div className='header-section'>
        <img src={logo} alt="logo" />
      </div>
      <Container component="main" maxWidth="xs">
        <Box sx={{ marginTop: 8, display: 'flex', flexDirection: 'column', alignItems: 'center' }}>
          <Paper elevation={3} sx={{ p: 4, width: '100%' }}>
            <Typography component="h1" variant="h5" sx={{ textAlign: 'center', mb: 3 }}>
              Login
            </Typography>
            {error && <Alert severity="error" sx={{ mb: 2 }}>{error}</Alert>}
            <Box component="form" onSubmit={onSubmit} sx={{ mt: 1 }}>
              <TextField margin="normal" required fullWidth id="email" label="Email Address" name="email"
                autoComplete="email" autoFocus value={email} onChange={onChange} disabled={loading} />
              <TextField margin="normal" required fullWidth name="password" label="Password" type="password"
                id="password" autoComplete="current-password" value={password} onChange={onChange} disabled={loading} />
              <Button type="submit" fullWidth variant="contained" sx={{ mt: 3, mb: 2, bgcolor: '#00A86B' }}
                disabled={loading}>
                {loading ? 'Logging in...' : 'Login'}
              </Button>
              <Button fullWidth variant="contained" color="secondary" onClick={handleGoogleSignIn} disabled={loading}>
                Sign in with Google
              </Button>
              <Box sx={{ textAlign: 'center', mt: 2 }}>
                <Link component="button" variant="body2" onClick={() => navigate('/forgot-password')}
                  sx={{
                    color: '#00A86B',
                    '&:hover': {
                      backgroundColor: 'transparent',
                      color: '#002d62',
                    },
                  }}disabled={loading}>
                  Forgot Password?
                </Link>
              </Box>
              <Box sx={{ textAlign: 'center', mt: 2 }}>
                <Link component="button" variant="body2" onClick={() => navigate('/signup')}
                  sx={{
                    color: '#00A86B',
                    '&:hover': {
                      backgroundColor: 'transparent',
                      color: '#002d62',
                    },
                  }}disabled={loading}>
                  Don't have an account? Sign up
                </Link>
              </Box>
            </Box>
          </Paper>
        </Box>
      </Container>
    </div>
  );
};

export default Login;
